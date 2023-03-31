# Task 1 – Understand Cgroups Version One

## Subtask 1.1 – Default System Configuration
**Analyze the default cgroup configuration of Ubuntu. Which subsystems are supported by the Ubuntu kernel? Explain the output.**

How to list the supported subsystems:
```console
$ cat /proc/cgroups

#subsys_name   hierarchy  num_cgroups  enabled
cpuset         4          1            1
cpu            3          92           1
cpuacct        3          92           1
blkio          7          92           1
memory         11         153          1
devices        6          92           1
freezer        8          2            1
net_cls        9          1            1
perf_event     5          1            1
net_prio       9          1            1
hugetlb        10         1            1
pids           12         98           1
rdma           2          1            1
```

- cpu: This subsystem controls CPU usage and can be used to limit the amount of CPU time that a cgroup or process can consume.
- cpuset: This subsystem assigns CPUs and memory nodes to cgroups and processes, which can be useful in a multi-processor system.
- memory: This subsystem manages memory usage and can be used to limit the amount of memory that a cgroup or process can consume.
- blkio: This subsystem controls input/output (I/O) access to block devices, such as hard disk drives, and can be used to limit the amount of I/O that a cgroup or process can perform.
- devices: This subsystem controls access to devices, such as USB drives, and can be used to restrict device access for specific cgroups or processes.
- freezer: This subsystem suspends or resumes processes in a cgroup, which can be useful for pausing or resuming system services or applications.

Alternatively one can list the files in cgroup:
```console
$ cd /sys/fs/cgroup/
$ ls -lha

total 0
drwxr-xr-x 15 root root 380 Mar 30 18:48 .
drwxr-xr-x  9 root root   0 Mar 30 18:48 ..
dr-xr-xr-x  5 root root   0 Mar 30 18:48 blkio
lrwxrwxrwx  1 root root  11 Mar 30 18:48 cpu -> cpu,cpuacct
dr-xr-xr-x  5 root root   0 Mar 30 18:48 cpu,cpuacct
lrwxrwxrwx  1 root root  11 Mar 30 18:48 cpuacct -> cpu,cpuacct
dr-xr-xr-x  2 root root   0 Mar 30 18:48 cpuset
dr-xr-xr-x  5 root root   0 Mar 30 18:48 devices
dr-xr-xr-x  3 root root   0 Mar 30 18:48 freezer
dr-xr-xr-x  2 root root   0 Mar 30 18:48 hugetlb
dr-xr-xr-x  5 root root   0 Mar 30 18:48 memory
lrwxrwxrwx  1 root root  16 Mar 30 18:48 net_cls -> net_cls,net_prio
dr-xr-xr-x  2 root root   0 Mar 30 18:48 net_cls,net_prio
lrwxrwxrwx  1 root root  16 Mar 30 18:48 net_prio -> net_cls,net_prio
dr-xr-xr-x  2 root root   0 Mar 30 18:48 perf_event
dr-xr-xr-x  5 root root   0 Mar 30 18:48 pids
dr-xr-xr-x  2 root root   0 Mar 30 18:48 rdma
dr-xr-xr-x  5 root root   0 Mar 30 18:48 systemd
dr-xr-xr-x  5 root root   0 Mar 30 18:48 unified
```
**Which hierarchies are provided by default? Which subsystems are configured at which hierarchy?**

TODO

**Navigate to the default CPU hierarchy and check how many cgroups are present. What may be the purpose of these cgroups? Who created them? You may find the command systemd-cgls useful.**

```console
$ cd /sys/fs/cgroup/cpu
$ systemd-cgls cpu

Controller cpu; Control group /:
├─user.slice
│ ├─14620 sshd: ubuntu [priv]
│ ├─14623 /lib/systemd/systemd --user
│ ├─14624 (sd-pam)
│ ├─14741 sshd: ubuntu@pts/0
│ ├─14742 -bash
│ ├─15242 systemd-cgls cpu
│ └─15243 pager
├─init.scope
│ └─1 /sbin/init
└─system.slice
  ├─irqbalance.service
  │ └─736 /usr/sbin/irqbalance --foreground
  ├─systemd-networkd.service
  │ └─611 /lib/systemd/systemd-networkd
  ├─systemd-udevd.service
  │ └─403 /lib/systemd/systemd-udevd
  ├─cron.service
  │ └─722 /usr/sbin/cron -f
  ├─system-serial\x2dgetty.slice
  │ └─763 /sbin/agetty -o -p -- \u --keep-baud 115200,38400,9600 ttyS0 vt220
  ├─polkit.service
  │ └─800 /usr/lib/policykit-1/polkitd --no-debug
  ├─networkd-dispatcher.service
  │ └─738 /usr/bin/python3 /usr/bin/networkd-dispatcher --run-startup-triggers
  ├─multipathd.service
  │ └─503 /sbin/multipathd -d -s
  ├─accounts-daemon.service
  │ └─719 /usr/lib/accountsservice/accounts-daemon
  ├─systemd-journald.service
  │ └─367 /lib/systemd/systemd-journald
  ├─atd.service
  │ └─749 /usr/sbin/atd -f
  ├─ssh.service
  │ └─854 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
  ├─snapd.service
  │ └─13758 /usr/lib/snapd/snapd
  ├─rsyslog.service
  │ └─744 /usr/sbin/rsyslogd -n -iNONE
  ├─systemd-resolved.service
  │ └─628 /lib/systemd/systemd-resolved
  ├─udisks2.service
  │ └─748 /usr/lib/udisks2/udisksd
  ├─dbus.service
  │ └─728 /usr/bin/dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
  ├─systemd-timesyncd.service
  │ └─536 /lib/systemd/systemd-timesyncd
  ├─system-getty.slice
  │ └─769 /sbin/agetty -o -p -- \u --noclear tty1 linux
  └─systemd-logind.service
    └─746 /lib/systemd/systemd-logind
```

The purpose of these cgroups is to manage CPU resource allocation and utilization for different processes and groups of processes. By creating separate cgroups for different processes or groups of processes, system administrators can enforce resource limits, prioritize CPU usage, and prevent certain processes from consuming too much CPU time and impacting system performance.

The cgroups under the CPU hierarchy can be created by different system components, such as systemd or other process management tools. Depending on the system configuration and workload, there may be multiple cgroups present under the CPU hierarchy, each serving a specific purpose or grouping of processes.

**How many processes are in cgroup user.slice/system.slice/init.slice?**

- user.slice: 9
- system.slice: 19
- init.slice: 1

Count from the tree in previous view or pipe grep:

```console
$ systemd-cgls /user.slice | grep "├─" | wc -l
9
$ /sys/fs/cgroup/cpu$ systemd-cgls /system.slice | grep "├─" | wc -l
19
```

**Run the following commands and explain the output: ps xawf -eo pid,user,cgroup,args**

```console
$ ps xawf -eo pid,user,cgroup,args
    PID USER     CGROUP                      COMMAND
      2 root     -                           [kthreadd]
      3 root     -                            \_ [rcu_gp]
      4 root     -                            \_ [rcu_par_gp]
      6 root     -                            \_ [kworker/0:0H-kblockd]
      9 root     -                            \_ [mm_percpu_wq]
     10 root     -                            \_ [ksoftirqd/0]
     11 root     -                            \_ [rcu_sched]
     12 root     -                            \_ [migration/0]
     13 root     -                            \_ [idle_inject/0]
     14 root     -                            \_ [cpuhp/0]
     15 root     -                            \_ [cpuhp/1]
     16 root     -                            \_ [idle_inject/1]
     17 root     -                            \_ [migration/1]
     18 root     -                            \_ [ksoftirqd/1]
     20 root     -                            \_ [kworker/1:0H]
     21 root     -                            \_ [cpuhp/2]
     22 root     -                            \_ [idle_inject/2]
     23 root     -                            \_ [migration/2]
     24 root     -                            \_ [ksoftirqd/2]
     26 root     -                            \_ [kworker/2:0H-kblockd]
     27 root     -                            \_ [cpuhp/3]
     28 root     -                            \_ [idle_inject/3]
     29 root     -                            \_ [migration/3]
     30 root     -                            \_ [ksoftirqd/3]
     32 root     -                            \_ [kworker/3:0H-kblockd]
     33 root     -                            \_ [kdevtmpfs]
     34 root     -                            \_ [netns]
     35 root     -                            \_ [rcu_tasks_kthre]
     36 root     -                            \_ [kauditd]
     37 root     -                            \_ [khungtaskd]
     38 root     -                            \_ [oom_reaper]
     39 root     -                            \_ [writeback]
     40 root     -                            \_ [kcompactd0]
     41 root     -                            \_ [ksmd]
     42 root     -                            \_ [khugepaged]
     90 root     -                            \_ [kintegrityd]
     91 root     -                            \_ [kblockd]
     92 root     -                            \_ [blkcg_punt_bio]
     93 root     -                            \_ [tpm_dev_wq]
     94 root     -                            \_ [ata_sff]
     95 root     -                            \_ [md]
     96 root     -                            \_ [edac-poller]
     97 root     -                            \_ [devfreq_wq]
     98 root     -                            \_ [watchdogd]
    102 root     -                            \_ [kswapd0]
    103 root     -                            \_ [ecryptfs-kthrea]
    105 root     -                            \_ [kthrotld]
    106 root     -                            \_ [acpi_thermal_pm]
    107 root     -                            \_ [scsi_eh_0]
    108 root     -                            \_ [scsi_tmf_0]
    109 root     -                            \_ [scsi_eh_1]
    110 root     -                            \_ [scsi_tmf_1]
    112 root     -                            \_ [vfio-irqfd-clea]
    113 root     -                            \_ [ipv6_addrconf]
    122 root     -                            \_ [kstrp]
    125 root     -                            \_ [kworker/u9:0]
    138 root     -                            \_ [charger_manager]
    188 root     -                            \_ [cryptd]
    252 root     -                            \_ [raid5wq]
    292 root     -                            \_ [jbd2/vda1-8]
    293 root     -                            \_ [ext4-rsv-conver]
    328 root     -                            \_ [hwrng]
    352 root     -                            \_ [kworker/2:1H-kblockd]
    388 root     -                            \_ [kworker/0:1H-kblockd]
    399 root     -                            \_ [kworker/3:1H-kblockd]
    400 root     -                            \_ [kworker/1:1H-kblockd]
    499 root     -                            \_ [kaluad]
    500 root     -                            \_ [kmpath_rdacd]
    501 root     -                            \_ [kmpathd]
    502 root     -                            \_ [kmpath_handlerd]
    513 root     -                            \_ [loop0]
    516 root     -                            \_ [loop1]
   1643 root     -                            \_ [kworker/0:0-events]
   1768 root     -                            \_ [kworker/2:3-events_freezable]
  13585 root     -                            \_ [loop3]
  13719 root     -                            \_ [loop4]
  13756 root     -                            \_ [kworker/1:0-cgroup_destroy]
  14112 root     -                            \_ [loop5]
  14204 root     -                            \_ [kworker/2:1-rcu_gp]
  14402 root     -                            \_ [kworker/3:0-events]
  14641 root     -                            \_ [kworker/0:1-cgroup_destroy]
  14777 root     -                            \_ [kworker/3:2]
  15153 root     -                            \_ [kworker/1:1-events]
  15230 root     -                            \_ [kworker/u8:0-events_unbound]
  15240 root     -                            \_ [kworker/u8:1-events_power_efficient]
      1 root     12:pids:/init.scope,11:memo /sbin/init
    367 root     12:pids:/system.slice/syste /lib/systemd/systemd-journald
    403 root     12:pids:/system.slice/syste /lib/systemd/systemd-udevd
    503 root     12:pids:/system.slice/multi /sbin/multipathd -d -s
    536 systemd+ 12:pids:/system.slice/syste /lib/systemd/systemd-timesyncd
    611 systemd+ 12:pids:/system.slice/syste /lib/systemd/systemd-networkd
    628 systemd+ 12:pids:/system.slice/syste /lib/systemd/systemd-resolved
    719 root     12:pids:/system.slice/accou /usr/lib/accountsservice/accounts-daemon
    722 root     12:pids:/system.slice/cron. /usr/sbin/cron -f
    728 message+ 12:pids:/system.slice/dbus. /usr/bin/dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activat
    736 root     12:pids:/system.slice/irqba /usr/sbin/irqbalance --foreground
    738 root     12:pids:/system.slice/netwo /usr/bin/python3 /usr/bin/networkd-dispatcher --run-startup-triggers
    744 syslog   12:pids:/system.slice/rsysl /usr/sbin/rsyslogd -n -iNONE
    746 root     12:pids:/system.slice/syste /lib/systemd/systemd-logind
    748 root     12:pids:/system.slice/udisk /usr/lib/udisks2/udisksd
    749 daemon   12:pids:/system.slice/atd.s /usr/sbin/atd -f
    763 root     12:pids:/system.slice/syste /sbin/agetty -o -p -- \u --keep-baud 115200,38400,9600 ttyS0 vt220
    769 root     12:pids:/system.slice/syste /sbin/agetty -o -p -- \u --noclear tty1 linux
    800 root     12:pids:/system.slice/polki /usr/lib/policykit-1/polkitd --no-debug
    854 root     12:pids:/system.slice/ssh.s sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
  14620 root     12:pids:/user.slice/user-10  \_ sshd: ubuntu [priv]
  14741 ubuntu   12:pids:/user.slice/user-10      \_ sshd: ubuntu@pts/0
  14742 ubuntu   12:pids:/user.slice/user-10          \_ -bash
  15247 ubuntu   12:pids:/user.slice/user-10              \_ systemd-cgls cpu
  15248 ubuntu   12:pids:/user.slice/user-10              |   \_ pager
  15302 ubuntu   12:pids:/user.slice/user-10              \_ ps xawf -eo pid,user,cgroup,args
  13758 root     12:pids:/system.slice/snapd /usr/lib/snapd/snapd
  14623 ubuntu   12:pids:/user.slice/user-10 /lib/systemd/systemd --user
  14624 ubuntu   12:pids:/user.slice/user-10  \_ (sd-pam)
```

The command lists all running processes along with their PID, username of the creator, cgroup name and command line arguments for starting the process.

The options xawf specify the following:
- x: list all processes when used together with the a option
- a: include processes from all users
- w: wide output format
- f: full format display

**Check the configuration for a process called “cron” using “ps” and “grep”. Explain both, the configuration for the “cron” and “ps” process, in terms of systemd and cgroup configuration.**

```console
$ ps aux | grep cron
root         722  0.0  0.0   8536  2868 ?        Ss   Mar30   0:00 /usr/sbin/cron -f
ubuntu     15306  0.0  0.0   8160   736 pts/0    S+   09:26   0:00 grep --color=auto cron
```
The output includes the PID, the username that started the process, the start time, the command line arguments.

In terms of systemd and cgroup configuration, cron is usually started as a systemd service, and its configuration is managed through a unit file located at /lib/systemd/system/cron.service. This file specifies the configuration options for the cron service, including the cgroup settings.

By default, the cron process is usually started in the system.slice cgroup, which is managed by systemd. This cgroup contains system-level services and is a parent cgroup for other system service cgroups.

```console
$ cat /lib/systemd/system/cron.service
[Unit]
Description=Regular background program processing daemon
Documentation=man:cron(8)
After=remote-fs.target nss-user-lookup.target

[Service]
EnvironmentFile=-/etc/default/cron
ExecStart=/usr/sbin/cron -f $EXTRA_OPTS
IgnoreSIGPIPE=false
KillMode=process
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

**Verify your understanding by using the following commands: systemd-cgtop and systemd-cgls**



**Can you identify the “cron” service?**


## Subtask 1.2 – Default System Configuration by Systemd
