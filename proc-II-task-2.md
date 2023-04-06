## Subtask 2 - Create Custom Controls
Create an environment to control CPU access (CPU affinity) from scratch, i.e. not using any preconfigured cgroups.
```console
$ sudo mkdir /mnt/myenv
```
Create a tmpfs under /mnt and use this directory for your cgroup hierarchy.
```console
$ sudo mount -t tmpfs myenv /mnt/myenv -o size=10M
```

How many CPUs can be controlled on your system per default?
```console
$ nproc
4
```
Number of cores the CPU has.

Create M processes, with M being the number of CPUs (N) on your system plus one (M=N+1). Those processes shall consume 100% CPU load.

4+1 = 5 processes
```console
$ for i in {1..5}; do dd if=/dev/urandom of=/dev/null & done
[1] 23649
[2] 23650
[3] 23651
[4] 23652
[5] 23653
```

```console
$ top

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  23651 ubuntu    20   0    7272    788    720 R  89.7   0.0   2:23.90 dd
  23652 ubuntu    20   0    7272    720    656 R  83.7   0.0   2:22.27 dd
  23653 ubuntu    20   0    7272    772    708 R  80.0   0.0   2:23.53 dd
  23649 ubuntu    20   0    7272    784    720 R  73.7   0.0   2:21.90 dd
```

Now configure your cgroups to only allow these processes to use half of the CPUs available on your system.
Mind the following prerequisite (command to be executed once the controller is mounted and the cgroup created): echo 0 > cpuset.mems
```console
$ echo 0 | sudo tee /mnt/myenv/cpuset.mems
$ echo "0-1" | sudo tee /mnt/myenv/cpuset.cpus
$ sudo echo 23649 >> /mnt/myenv/cgroup.procs
$ sudo echo 23650 >> /mnt/myenv/cgroup.procs
$ sudo echo 23651 >> /mnt/myenv/cgroup.procs
$ sudo echo 23652 >> /mnt/myenv/cgroup.procs
$ sudo echo 23653 >> /mnt/myenv/cgroup.procs
$ cat cgroup.procs
23649
23650
23651
23652
23653
$ sudo echo 0 > /mnt/myenv/cpuset.mems
```

Verify and explain the effect by looking at the processes via "top".
```console
$ top
    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  23651 ubuntu    20   0    7272    788    720 R  50.2   0.0  70:41.55 dd
  23649 ubuntu    20   0    7272    784    720 R  49.8   0.0  70:37.21 dd
  23650 ubuntu    20   0    7272    788    720 R  33.2   0.0  70:37.68 dd
  23652 ubuntu    20   0    7272    720    656 R  33.2   0.0  70:39.50 dd
  23653 ubuntu    20   0    7272    772    708 R  33.2   0.0  70:40.75 dd
```

Also double-check the CPU assignments (affinity) with the command "taskset".
```console
$ taskset -pc 23649
pid 23649's current affinity list: 0,1
$ taskset -pc 23650
pid 23650's current affinity list: 0,1
$ taskset -pc 23651
pid 23651's current affinity list: 0,1
$ taskset -pc 23652
pid 23652's current affinity list: 0,1
$ taskset -pc 23653
pid 23653's current affinity list: 0,1
```

Disable those CPUs via "chcpu", that you allowed in your cpuset and explain what happens with those processes pinned onto them.
```console
$ sudo chcpu -d 0,1
```

verify available cpus
```console
$ lscpu
```

The processes will get migrated to cpu that is still available. if no CPUs are available in the cpuset the processes will not be able to execute until cpu becomes available.
The set default core however cannot be disabled so processes can still run.