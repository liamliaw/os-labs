# Lab 2

**Members: Fangwen Liao (3439869) | Yijin Wang (3476217) | Moritz Pfeffer (3261671)**

## Exercise 1

*(a) Use only the ps command to list all processes (including UID) with parent process ID (PPID) 1.*  
I consult the man page of ps and find the **--ppid** switch.
> --ppid pidlist  
> Select by parent process ID.  This selects the processes with a parent process ID in pidlist.

To include the UID I use the u option, and to print it numerically I use the n modifier.
Both are documented in the man pages of ps.
> u      Display user-oriented format.  
> n      Numeric output for WCHAN and USER (including all types of UID and GID).

```console
moritzpfeffer@debian:~$ ps --ppid 1 nu
    USER   PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
       0   212  0.0  0.1  11776  6712 ?        Ss   14:45   0:01 /lib/systemd/systemd-journald
       0   241  0.0  0.0  21868  1528 ?        Ss   14:45   0:00 /sbin/lvmetad -f
       0   242  0.0  0.1  16240  4100 ?        Ss   14:45   0:00 /lib/systemd/systemd-udevd
     115   431  0.0  0.0   6252  3116 ?        Ss   14:45   0:00 avahi-daemon: running [debian.local]
       0   432  0.0  0.0   6612  1940 ?        Ss   14:45   0:00 /usr/sbin/irqbalance --foreground
       0   433  0.0  0.1   7456  4716 ?        Ss   14:45   0:00 /lib/systemd/systemd-logind
     108   436  0.0  0.1   7244  4648 ?        Ss   14:45   0:02 /usr/bin/dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation
     113   461  0.0  0.0  24104  3044 ?        SNsl 14:45   0:00 /usr/lib/rtkit/rtkit-daemon
       0   462  0.0  0.4 101864 15860 ?        Ssl  14:45   0:00 /usr/sbin/NetworkManager --no-daemon
       0   465  0.0  0.1  39360  6424 ?        Ssl  14:45   0:00 /usr/lib/accountsservice/accounts-daemon
       0   466  0.0  0.0  23528  2996 ?        Ssl  14:45   0:00 /usr/sbin/rsyslogd -n
       0   468  0.0  0.2  51904  8392 ?        Ssl  14:45   0:00 /usr/sbin/ModemManager
       0   486  0.0  0.2  39616  8200 ?        Ssl  14:45   0:00 /usr/lib/policykit-1/polkitd --no-debug
       0   539  0.0  0.1  10496  5160 ?        Ss   14:45   0:00 /usr/sbin/sshd -D
       0   710  0.0  0.2  49556  7568 ?        Ssl  14:45   0:00 /usr/sbin/gdm3
       0   717  0.0  0.0  31868  2988 ?        Sl   14:45   0:03 /usr/sbin/VBoxService --pidfile /var/run/vboxadd-service.sh
     118   733  0.0  0.1   9552  6092 ?        Ss   14:45   0:00 /lib/systemd/systemd --user
       0   741  0.0  0.0   2100    52 ?        Ss   14:45   0:00 /usr/sbin/minissdpd -i 0.0.0.0
       0  1003  0.0  0.2  51332  7736 ?        Ssl  14:45   0:00 /usr/lib/upower/upowerd
     105  1014  0.0  0.0  11572  3244 ?        Ss   14:45   0:00 /usr/sbin/exim4 -bd -q30m
       0  1051  0.0  0.1  10796  4472 ?        Ss   14:45   0:00 /sbin/wpa_supplicant -u -s -O /run/wpa_supplicant
       0  1052  0.0  0.3  64484 13244 ?        Ssl  14:45   0:00 /usr/lib/packagekit/packagekitd
     116  1075  0.0  0.3  46728 13368 ?        Ssl  14:45   0:00 /usr/lib/colord/colord
    1000  1095  0.0  0.1   9552  6152 ?        Ss   14:45   0:00 /lib/systemd/systemd --user
    1000  1102  0.0  0.1  39280  4848 ?        Sl   14:45   0:00 /usr/bin/gnome-keyring-daemon --daemonize --login
    1000  1168  0.0  0.0  15808   308 ?        S    14:45   0:00 /usr/bin/VBoxClient --clipboard
    1000  1179  0.0  0.0  15808   308 ?        S    14:45   0:00 /usr/bin/VBoxClient --seamless
    1000  1186  0.0  0.0  15808   308 ?        S    14:45   0:00 /usr/bin/VBoxClient --draganddrop
    1000  1194  0.0  0.0  15808   308 ?        S    14:45   0:00 /usr/bin/VBoxClient --vmsvga
    1000  1257  0.5  0.3 888652 11828 ?        S<l  14:45   1:04 /usr/bin/pulseaudio --start --log-target=syslog
    1000  1368  0.0  0.2  71848 10472 tty2     Sl+  14:45   0:00 /usr/lib/gnome-settings-daemon/gsd-printer
    1000  1378  0.0  0.0  15808  1368 ?        S    14:45   0:01 /usr/bin/VBoxClient --vmsvga
       0  6174  0.0  0.0   5256  2864 ?        Ss   15:38   0:00 /usr/sbin/cron -f
       0 11456  0.0  0.2  55620  7480 ?        Ssl  17:19   0:00 /usr/lib/udisks2/udisksd --no-debug
       0 11994  0.0  0.1  14656  7172 ?        Ss   17:24   0:00 /usr/sbin/cupsd -l
```

*(b) Filter the list of processes using grep (or awk) such that only processes no kernel threads, e.g. [ kworker ]]) running as username root remain.*  
I consult the man page of ps and find the **--user** switch.
> --user userlist  
> Select by effective user ID (EUID) or name.  Identical to -u and U.

Like in (a) I use **nu** and pipe the output into grep.  
Here I filter out lines with closing brackets using the **-v** switch.  
These correspond to kernel threads for the following reason:  
[A post on stackexchange](https://unix.stackexchange.com/questions/22121/what-do-the-brackets-around-processes-mean) tells me that
ps prints the command in brackets when the process args are unavailable. [Another page](https://gtirloni.com/2017/12/ps-output-processes-with-brackets/) indicates that
"these will be kernel threads implementing helper functions, specific subsystems, work queues, etc."
<div style="page-break-after: always;"></div>

```console
moritzpfeffer@debian:~$ ps --user root nu | grep -v ]
    USER   PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
       0     1  0.0  0.1  28308  6552 ?        Ss   14:45   0:01 /sbin/init
       0   212  0.0  0.1  11776  6712 ?        Ss   14:45   0:01 /lib/systemd/systemd-journald
       0   241  0.0  0.0  21868  1528 ?        Ss   14:45   0:00 /sbin/lvmetad -f
       0   242  0.0  0.1  16240  4100 ?        Ss   14:45   0:00 /lib/systemd/systemd-udevd
       0   432  0.0  0.0   6612  1940 ?        Ss   14:45   0:00 /usr/sbin/irqbalance --foreground
       0   433  0.0  0.1   7456  4716 ?        Ss   14:45   0:00 /lib/systemd/systemd-logind
       0   462  0.0  0.4 101864 15860 ?        Ssl  14:45   0:00 /usr/sbin/NetworkManager --no-daemon
       0   465  0.0  0.1  39360  6424 ?        Ssl  14:45   0:00 /usr/lib/accountsservice/accounts-daemon
       0   466  0.0  0.0  23528  2996 ?        Ssl  14:45   0:00 /usr/sbin/rsyslogd -n
       0   468  0.0  0.2  51904  8392 ?        Ssl  14:45   0:00 /usr/sbin/ModemManager
       0   486  0.0  0.2  39616  8200 ?        Ssl  14:45   0:00 /usr/lib/policykit-1/polkitd --no-debug
       0   539  0.0  0.1  10496  5160 ?        Ss   14:45   0:00 /usr/sbin/sshd -D
       0   710  0.0  0.2  49556  7568 ?        Ssl  14:45   0:00 /usr/sbin/gdm3
       0   717  0.0  0.0  31868  2988 ?        Sl   14:45   0:03 /usr/sbin/VBoxService --pidfile /var/run/vboxadd-service.sh
       0   741  0.0  0.0   2100    52 ?        Ss   14:45   0:00 /usr/sbin/minissdpd -i 0.0.0.0
       0  1003  0.0  0.2  51332  7736 ?        Ssl  14:45   0:00 /usr/lib/upower/upowerd
       0  1051  0.0  0.1  10796  4472 ?        Ss   14:45   0:00 /sbin/wpa_supplicant -u -s -O /run/wpa_supplicant
       0  1052  0.0  0.3  64484 13244 ?        Ssl  14:45   0:00 /usr/lib/packagekit/packagekitd
       0  6174  0.0  0.0   5256  2864 ?        Ss   15:38   0:00 /usr/sbin/cron -f
       0 10735  0.0  0.1   8124  3744 ?        S    17:19   0:00 /sbin/dhclient -d -q -sf /usr/lib/NetworkManager/nm-dhcp-helper -pf /var/run/dhclient-enp0s3.pid -lf /var/lib/NetworkManager/dhclient-2b5ae656-5993-4431-82a4-da96c10b4d7e-enp0s3.lease -cf /var/lib/NetworkManager/dhclient-enp0s3.conf enp0s3
       0 11456  0.0  0.2  55620  7480 ?        Ssl  17:19   0:00 /usr/lib/udisks2/udisksd --no-debug
       0 11994  0.0  0.1  14656  7172 ?        Ss   17:24   0:00 /usr/sbin/cupsd -l
```

Then i verify that this really yields the expected number of processes.

```console
moritzpfeffer@debian:~$ ps --user root nu | grep -v ] | wc -l
23
```

23 - 1 (header line) = 22. This matches the number indicated in the exercise description of (c).  
Thus, my command appears to be correct.  
*(c) For each of the remaining (~22) processes, provide a 2-3 sentences describing the functionality of each process (Hint: use man / Arch)*  

```console
/usr/sbin/rsyslogd -n
> It provides support for message logging while avoiding auto-backgrounding.
/usr/sbin/ModemManager
> provides a unified high level API for communicating with mobile broadband modems, regardless of the protocol used to communicate with the actual device (Generic AT, vendor-specific AT, QCDM, QMI, MBIM...).
/usr/lib/policykit-1/polkitd --no-debug
> polkitd provides the org.freedesktop.PolicyKit1 D-Bus service on the system message bus. Users or administrators should never need to start this daemon as it will be automatically started by dbus-daemon(1) whenever an application calls into the service.
/usr/sbin/sshd -D
> provide secure encrypted communications between two untrusted hosts over an insecure network. When this option is specified, sshd will not detach and does not become a daemon.  This allows easy monitoring of sshd.
/usr/sbin/gdm3
> gdm3 reads /etc/gdm3/custom.conf for its configuration. For each local display, gdm starts an X server and runs a minimal GNOME session including a graphical greeter. If configured so, the main gdm process also listens for XDMCP requests from remote displays.
/usr/sbin/VBoxService --pidfile /var/run/vboxadd-service.sh
> Write the process ID to a file in /var/run/vboxadd-service.sh.
/usr/sbin/minissdpd -i 0.0.0.0
> It listens for SSDP traffic and keeps track of what are the UPnP devices up  on  the network while the name  or  IP address of the interface used to listen to SSDP packets coming on multicast address 0.0.0.0.
```

## Exercise 2

*(a) Run the binary and show the process tree using pstree and the parent PID.*
While the fork file is excuting, switch to another tty:

```console
fangwenliao@debian:~/Downloads/OS_Lab2/fork$ make
gcc -w -Werror -o fork fork.c
fangwenliao@debian:~/Downloads/OS_Lab2/fork$ ./fork
Main process PID: 1845
Child PID: 1848
Child PID: 1847
Child PID: 1846
Child PID: 1850
Child PID: 1849
Child PID: 1851
Child PID: 1852
Press ENTER key to Continue

Process 1847 ended

Process 1846 ended

Process 1848 ended

Process 1852 ended

Process 1850 ended

Process 1851 ended

Process 1849 ended

Process 1845 ended
```

Meanwhile switch to another TTY, and run:

```console
fangwenliao@debian:~$ pstree -p 1845
fork(1845)─┬─fork(1846)─┬─fork(1849)───fork(1852)
           │            └─fork(1851)
           ├─fork(1847)───fork(1850)
           └─fork(1848)
```

*(b) Explain the number of running processes based on the source code in fork.c.*
According to man page of fork, the fork() copies the calling process in a seperate memory space and the child process has the same content.
In the source code there are 3 fork() calls. When process 1845 fork for the first time, it creates 1846, at this point both 1845 and 1846 will run the second fork.
In the second fork, 1845 and 1846 create correspondly 1849 and 1847, after that 1845, 1846, 1847 1849 are prepared for the third fork.
Finally 1852, 1851, 1850 1848 are forked.  
*(c) Do these processes share memory or other resources? Why (not)?*
Arrording to man page of fork, they are in different memory space, however fork() in Linux use copy-to-write technique, they actually share the same physical memory, because they didn't write anything.

## Exercise 3

*(a) Implement an application in C that uses (1) a clone() system call to create a process, (2) a clone() system call to create a thread, and (3) a fork() (in that specific order)*
```c
#include <stdio.h> // printf()
#include <unistd.h> // sleep(), getpid(), getppid()
#include <signal.h> // SIGCHLD flag
#include <linux/sched.h> // CLONE flags

void printids(void) {
  printf("TGID: %d\n", getpid()); // Print the TGID of the current process
  printf("PPID: %d\n", getppid()); // Print the PPID of the current process  
  printf("\n\n");
  sleep(1);
}

int child(void *arg) 
{
  printids();
}


int main(void)
{
  printf("This is the parent process.\n");
  printids(); // IDs of the main process
  getchar();

  printf("This is a child process created by clone().\n");
  void *child_stack;
  child_stack = (void*)malloc(1024);
  clone(&child, child_stack+1024, SIGCHLD, 0);
  getchar(); // Press a key to continue

  printf("This is a thread created by clone().\n");
  clone(&child, child_stack+2048, CLONE_VM|CLONE_FS|CLONE_FILES|CLONE_SIGHAND, 0);
  getchar();

  printf("This is a fork().\n");
  fork();
  getchar();

  return 0;
}
```

*(b) For each of the created processes and threads, print and clearly show the TGID and PPID.*

```console
fangwenliao@debian:~/Downloads/OS_Lab2/clone$ ./clone 
This is the parent process.
TGID: 1739
PPID: 1676



This is a child process created by clone().
TGID: 1743
PPID: 1739



This is a thread created by clone().
TGID: 1744
PPID: 1739



This is a fork().
```

```console
fangwenliao@debian:~/Downloads/OS_Lab2/clone$ pstree -pg 1739
clone(1739,1739)─┬─clone(1743,1739)
                 ├─clone(1744,1739)
                 └─clone(1745,1739)
```

*(c) Explain why each of the TGIDs and PPIDs have the values as shown.*

The TGID of the first one is also used as process ID. The other three processes are all created by the first process, so they share the same PPID which is the PID of the first one.
The three child processes fully copy the parent process, each process is guaranteed a unique TGID used to identify.

## Exercise 4

*(a) Implement the list_processes function such that it outputs (prints) a list of all processes including the executable name, process ID, and thread group ID.*  

```c
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/sched.h>

#define M_AUTHOR "Moritz Pfeffer <st152880@stud.uni-stuttgart.de>"
#define M_DESC "Process Module"

void list_processes(struct task_struct *task) // task_struct is defined in <linux/sched.h>
{
    printk(KERN_INFO "name\tpid\ttgid");
    for_each_process(task)
    { // for_each_process is a helper method defined in <linux/sched.h>
        // TODO Implement: for each task print the executable name, PID, and TGID
        printk(KERN_INFO "%s\t%d\t%d", task->comm, task->pid, task->tgid);
    }
}

int init(void)
{
    printk(KERN_INFO "pm: init(void)\n");

    printk(KERN_INFO "pm: Module author = %s\n", M_AUTHOR);
    printk(KERN_INFO "pm: Module description = %s\n", M_DESC);

    list_processes(&init_task);

    return 0;
}

void exit(void)
{
    printk(KERN_INFO "pm: exit(void)\n");
}

module_init(init); // Define module entry point
module_exit(exit); // Define module exit point

MODULE_LICENSE("GPL");
MODULE_AUTHOR(M_AUTHOR);
MODULE_DESCRIPTION(M_DESC);
```

*(b) Show the output of the kern.log related to your module.*  

```console
Nov 16 21:34:27 debian kernel: [ 3190.100260] sudo	7788	7788
Nov 16 21:34:27 debian kernel: [ 3190.100260] insmod	7789	7789
Nov 16 21:35:57 debian kernel: [ 3280.526929] pm: exit(void)
Nov 16 21:35:59 debian kernel: [ 3282.503447] pm: init(void)
Nov 16 21:35:59 debian kernel: [ 3282.503448] pm: Module author = Moritz Pfeffer <st152880@stud.uni-stuttgart.de>
Nov 16 21:35:59 debian kernel: [ 3282.503448] pm: Module description = Process Module
Nov 16 21:35:59 debian kernel: [ 3282.503448] name	pid	tgid
Nov 16 21:35:59 debian kernel: [ 3282.503449] systemd	1	1
Nov 16 21:35:59 debian kernel: [ 3282.503450] kthreadd	2	2
Nov 16 21:35:59 debian kernel: [ 3282.503450] ksoftirqd/0	3	3
Nov 16 21:35:59 debian kernel: [ 3282.503451] kworker/0:0H	5	5
Nov 16 21:35:59 debian kernel: [ 3282.503451] rcu_sched	7	7
Nov 16 21:35:59 debian kernel: [ 3282.503452] rcu_bh	8	8
Nov 16 21:35:59 debian kernel: [ 3282.503452] migration/0	9	9
Nov 16 21:35:59 debian kernel: [ 3282.503453] lru-add-drain	10	10
Nov 16 21:35:59 debian kernel: [ 3282.503453] watchdog/0	11	11
Nov 16 21:35:59 debian kernel: [ 3282.503454] cpuhp/0	12	12
Nov 16 21:35:59 debian kernel: [ 3282.503454] cpuhp/1	13	13
Nov 16 21:35:59 debian kernel: [ 3282.503455] watchdog/1	14	14
Nov 16 21:35:59 debian kernel: [ 3282.503455] migration/1	15	15
Nov 16 21:35:59 debian kernel: [ 3282.503456] ksoftirqd/1	16	16
Nov 16 21:35:59 debian kernel: [ 3282.503456] kworker/1:0H	18	18
Nov 16 21:35:59 debian kernel: [ 3282.503457] kdevtmpfs	19	19
Nov 16 21:35:59 debian kernel: [ 3282.503457] netns	20	20
Nov 16 21:35:59 debian kernel: [ 3282.503458] khungtaskd	21	21
Nov 16 21:35:59 debian kernel: [ 3282.503458] oom_reaper	22	22
Nov 16 21:35:59 debian kernel: [ 3282.503459] writeback	23	23
Nov 16 21:35:59 debian kernel: [ 3282.503459] kcompactd0	24	24
Nov 16 21:35:59 debian kernel: [ 3282.503460] ksmd	26	26
Nov 16 21:35:59 debian kernel: [ 3282.503461] khugepaged	27	27
Nov 16 21:35:59 debian kernel: [ 3282.503461] crypto	28	28
Nov 16 21:35:59 debian kernel: [ 3282.503462] kintegrityd	29	29
Nov 16 21:35:59 debian kernel: [ 3282.503462] bioset	30	30
Nov 16 21:35:59 debian kernel: [ 3282.503463] kblockd	31	31
Nov 16 21:35:59 debian kernel: [ 3282.503463] devfreq_wq	32	32
Nov 16 21:35:59 debian kernel: [ 3282.503464] watchdogd	33	33
Nov 16 21:35:59 debian kernel: [ 3282.503464] kswapd0	34	34
Nov 16 21:35:59 debian kernel: [ 3282.503465] vmstat	35	35
Nov 16 21:35:59 debian kernel: [ 3282.503466] kthrotld	47	47
Nov 16 21:35:59 debian kernel: [ 3282.503466] ipv6_addrconf	48	48
Nov 16 21:35:59 debian kernel: [ 3282.503466] ata_sff	85	85
Nov 16 21:35:59 debian kernel: [ 3282.503467] scsi_eh_0	87	87
Nov 16 21:35:59 debian kernel: [ 3282.503467] scsi_tmf_0	89	89
Nov 16 21:35:59 debian kernel: [ 3282.503468] scsi_eh_1	91	91
Nov 16 21:35:59 debian kernel: [ 3282.503468] scsi_tmf_1	92	92
Nov 16 21:35:59 debian kernel: [ 3282.503469] bioset	109	109
Nov 16 21:35:59 debian kernel: [ 3282.503469] kworker/1:1H	112	112
Nov 16 21:35:59 debian kernel: [ 3282.503504] scsi_eh_2	114	114
Nov 16 21:35:59 debian kernel: [ 3282.503505] scsi_tmf_2	115	115
Nov 16 21:35:59 debian kernel: [ 3282.503506] scsi_eh_3	116	116
Nov 16 21:35:59 debian kernel: [ 3282.503506] scsi_tmf_3	117	117
Nov 16 21:35:59 debian kernel: [ 3282.503506] scsi_eh_4	118	118
Nov 16 21:35:59 debian kernel: [ 3282.503507] scsi_tmf_4	119	119
Nov 16 21:35:59 debian kernel: [ 3282.503507] scsi_eh_5	120	120
Nov 16 21:35:59 debian kernel: [ 3282.503508] scsi_tmf_5	121	121
Nov 16 21:35:59 debian kernel: [ 3282.503508] scsi_eh_6	122	122
Nov 16 21:35:59 debian kernel: [ 3282.503508] scsi_tmf_6	123	123
Nov 16 21:35:59 debian kernel: [ 3282.503509] scsi_eh_7	124	124
Nov 16 21:35:59 debian kernel: [ 3282.503509] scsi_tmf_7	125	125
Nov 16 21:35:59 debian kernel: [ 3282.503510] scsi_eh_8	126	126
Nov 16 21:35:59 debian kernel: [ 3282.503510] scsi_tmf_8	127	127
Nov 16 21:35:59 debian kernel: [ 3282.503511] scsi_eh_9	128	128
Nov 16 21:35:59 debian kernel: [ 3282.503511] scsi_tmf_9	129	129
Nov 16 21:35:59 debian kernel: [ 3282.503511] scsi_eh_10	130	130
Nov 16 21:35:59 debian kernel: [ 3282.503512] scsi_tmf_10	131	131
Nov 16 21:35:59 debian kernel: [ 3282.503512] scsi_eh_11	132	132
Nov 16 21:35:59 debian kernel: [ 3282.503513] scsi_tmf_11	133	133
Nov 16 21:35:59 debian kernel: [ 3282.503513] scsi_eh_12	134	134
Nov 16 21:35:59 debian kernel: [ 3282.503514] scsi_tmf_12	135	135
Nov 16 21:35:59 debian kernel: [ 3282.503514] scsi_eh_13	136	136
Nov 16 21:35:59 debian kernel: [ 3282.503515] scsi_tmf_13	137	137
Nov 16 21:35:59 debian kernel: [ 3282.503515] scsi_eh_14	138	138
Nov 16 21:35:59 debian kernel: [ 3282.503515] scsi_tmf_14	139	139
Nov 16 21:35:59 debian kernel: [ 3282.503516] scsi_eh_15	140	140
Nov 16 21:35:59 debian kernel: [ 3282.503516] scsi_tmf_15	141	141
Nov 16 21:35:59 debian kernel: [ 3282.503517] scsi_eh_16	142	142
Nov 16 21:35:59 debian kernel: [ 3282.503517] scsi_tmf_16	143	143
Nov 16 21:35:59 debian kernel: [ 3282.503517] scsi_eh_17	144	144
Nov 16 21:35:59 debian kernel: [ 3282.503518] scsi_tmf_17	145	145
Nov 16 21:35:59 debian kernel: [ 3282.503518] scsi_eh_18	146	146
Nov 16 21:35:59 debian kernel: [ 3282.503519] scsi_tmf_18	147	147
Nov 16 21:35:59 debian kernel: [ 3282.503520] scsi_eh_19	148	148
Nov 16 21:35:59 debian kernel: [ 3282.503520] scsi_tmf_19	149	149
Nov 16 21:35:59 debian kernel: [ 3282.503520] scsi_eh_20	150	150
Nov 16 21:35:59 debian kernel: [ 3282.503521] scsi_tmf_20	151	151
Nov 16 21:35:59 debian kernel: [ 3282.503521] scsi_eh_21	152	152
Nov 16 21:35:59 debian kernel: [ 3282.503521] scsi_tmf_21	153	153
Nov 16 21:35:59 debian kernel: [ 3282.503522] scsi_eh_22	154	154
Nov 16 21:35:59 debian kernel: [ 3282.503522] scsi_tmf_22	155	155
Nov 16 21:35:59 debian kernel: [ 3282.503523] scsi_eh_23	156	156
Nov 16 21:35:59 debian kernel: [ 3282.503523] scsi_tmf_23	157	157
Nov 16 21:35:59 debian kernel: [ 3282.503524] scsi_eh_24	158	158
Nov 16 21:35:59 debian kernel: [ 3282.503524] scsi_tmf_24	159	159
Nov 16 21:35:59 debian kernel: [ 3282.503524] scsi_eh_25	160	160
Nov 16 21:35:59 debian kernel: [ 3282.503525] scsi_tmf_25	161	161
Nov 16 21:35:59 debian kernel: [ 3282.503525] scsi_eh_26	162	162
Nov 16 21:35:59 debian kernel: [ 3282.503526] scsi_tmf_26	163	163
Nov 16 21:35:59 debian kernel: [ 3282.503526] scsi_eh_27	164	164
Nov 16 21:35:59 debian kernel: [ 3282.503526] scsi_tmf_27	165	165
Nov 16 21:35:59 debian kernel: [ 3282.503527] scsi_eh_28	166	166
Nov 16 21:35:59 debian kernel: [ 3282.503527] scsi_tmf_28	167	167
Nov 16 21:35:59 debian kernel: [ 3282.503528] scsi_eh_29	168	168
Nov 16 21:35:59 debian kernel: [ 3282.503528] scsi_tmf_29	169	169
Nov 16 21:35:59 debian kernel: [ 3282.503529] scsi_eh_30	170	170
Nov 16 21:35:59 debian kernel: [ 3282.503529] scsi_tmf_30	171	171
Nov 16 21:35:59 debian kernel: [ 3282.503529] scsi_eh_31	172	172
Nov 16 21:35:59 debian kernel: [ 3282.503530] scsi_tmf_31	173	173
Nov 16 21:35:59 debian kernel: [ 3282.503530] bioset	201	201
Nov 16 21:35:59 debian kernel: [ 3282.503531] kworker/0:1H	203	203
Nov 16 21:35:59 debian kernel: [ 3282.503531] kdmflush	207	207
Nov 16 21:35:59 debian kernel: [ 3282.503531] bioset	208	208
Nov 16 21:35:59 debian kernel: [ 3282.503532] kdmflush	211	211
Nov 16 21:35:59 debian kernel: [ 3282.503532] bioset	213	213
Nov 16 21:35:59 debian kernel: [ 3282.503533] kdmflush	216	216
Nov 16 21:35:59 debian kernel: [ 3282.503533] bioset	217	217
Nov 16 21:35:59 debian kernel: [ 3282.503534] kdmflush	220	220
Nov 16 21:35:59 debian kernel: [ 3282.503534] bioset	221	221
Nov 16 21:35:59 debian kernel: [ 3282.503534] kdmflush	226	226
Nov 16 21:35:59 debian kernel: [ 3282.503535] bioset	228	228
Nov 16 21:35:59 debian kernel: [ 3282.503536] kworker/u5:0	262	262
Nov 16 21:35:59 debian kernel: [ 3282.503536] jbd2/dm-0-8	277	277
Nov 16 21:35:59 debian kernel: [ 3282.503536] ext4-rsv-conver	278	278
Nov 16 21:35:59 debian kernel: [ 3282.503537] systemd-journal	308	308
Nov 16 21:35:59 debian kernel: [ 3282.503537] kauditd	314	314
Nov 16 21:35:59 debian kernel: [ 3282.503538] lvmetad	336	336
Nov 16 21:35:59 debian kernel: [ 3282.503538] systemd-udevd	341	341
Nov 16 21:35:59 debian kernel: [ 3282.503538] vmware-vmblock-	347	347
Nov 16 21:35:59 debian kernel: [ 3282.503539] vmtoolsd	348	348
Nov 16 21:35:59 debian kernel: [ 3282.503539] ttm_swap	416	416
Nov 16 21:35:59 debian kernel: [ 3282.503540] jbd2/dm-3-8	522	522
Nov 16 21:35:59 debian kernel: [ 3282.503540] ext4-rsv-conver	523	523
Nov 16 21:35:59 debian kernel: [ 3282.503541] jbd2/dm-4-8	529	529
Nov 16 21:35:59 debian kernel: [ 3282.503541] ext4-rsv-conver	530	530
Nov 16 21:35:59 debian kernel: [ 3282.503541] jbd2/dm-1-8	532	532
Nov 16 21:35:59 debian kernel: [ 3282.503542] ext4-rsv-conver	533	533
Nov 16 21:35:59 debian kernel: [ 3282.503542] ext4-rsv-conver	542	542
Nov 16 21:35:59 debian kernel: [ 3282.503543] VGAuthService	563	563
Nov 16 21:35:59 debian kernel: [ 3282.503543] rsyslogd	564	564
Nov 16 21:35:59 debian kernel: [ 3282.503544] irqbalance	565	565
Nov 16 21:35:59 debian kernel: [ 3282.503544] cron	566	566
Nov 16 21:35:59 debian kernel: [ 3282.503545] accounts-daemon	570	570
Nov 16 21:35:59 debian kernel: [ 3282.503545] cupsd	572	572
Nov 16 21:35:59 debian kernel: [ 3282.503546] systemd-logind	573	573
Nov 16 21:35:59 debian kernel: [ 3282.503546] dbus-daemon	574	574
Nov 16 21:35:59 debian kernel: [ 3282.503546] NetworkManager	626	626
Nov 16 21:35:59 debian kernel: [ 3282.503547] ModemManager	627	627
Nov 16 21:35:59 debian kernel: [ 3282.503547] rtkit-daemon	633	633
Nov 16 21:35:59 debian kernel: [ 3282.503548] avahi-daemon	634	634
Nov 16 21:35:59 debian kernel: [ 3282.503548] polkitd	638	638
Nov 16 21:35:59 debian kernel: [ 3282.503548] avahi-daemon	641	641
Nov 16 21:35:59 debian kernel: [ 3282.503549] cups-browsed	657	657
Nov 16 21:35:59 debian kernel: [ 3282.503549] dbus	658	658
Nov 16 21:35:59 debian kernel: [ 3282.503550] sshd	689	689
Nov 16 21:35:59 debian kernel: [ 3282.503550] gdm3	888	888
Nov 16 21:35:59 debian kernel: [ 3282.503551] gdm-session-wor	893	893
Nov 16 21:35:59 debian kernel: [ 3282.503551] systemd	897	897
Nov 16 21:35:59 debian kernel: [ 3282.503552] (sd-pam)	898	898
Nov 16 21:35:59 debian kernel: [ 3282.503552] gdm-wayland-ses	902	902
Nov 16 21:35:59 debian kernel: [ 3282.503553] dbus-daemon	904	904
Nov 16 21:35:59 debian kernel: [ 3282.503553] gnome-session-b	906	906
Nov 16 21:35:59 debian kernel: [ 3282.503554] gnome-shell	914	914
Nov 16 21:35:59 debian kernel: [ 3282.503581] upowerd	918	918
Nov 16 21:35:59 debian kernel: [ 3282.503582] Xwayland	932	932
Nov 16 21:35:59 debian kernel: [ 3282.503583] at-spi-bus-laun	937	937
Nov 16 21:35:59 debian kernel: [ 3282.503583] dbus-daemon	942	942
Nov 16 21:35:59 debian kernel: [ 3282.503584] at-spi2-registr	944	944
Nov 16 21:35:59 debian kernel: [ 3282.503584] pulseaudio	947	947
Nov 16 21:35:59 debian kernel: [ 3282.503585] wpa_supplicant	960	960
Nov 16 21:35:59 debian kernel: [ 3282.503585] packagekitd	961	961
Nov 16 21:35:59 debian kernel: [ 3282.503586] gnome-settings-	962	962
Nov 16 21:35:59 debian kernel: [ 3282.503586] colord	987	987
Nov 16 21:35:59 debian kernel: [ 3282.503587] minissdpd	1004	1004
Nov 16 21:35:59 debian kernel: [ 3282.503587] exim4	1252	1252
Nov 16 21:35:59 debian kernel: [ 3282.503588] gdm-session-wor	1279	1279
Nov 16 21:35:59 debian kernel: [ 3282.503588] systemd	1282	1282
Nov 16 21:35:59 debian kernel: [ 3282.503589] (sd-pam)	1283	1283
Nov 16 21:35:59 debian kernel: [ 3282.503589] gnome-keyring-d	1289	1289
Nov 16 21:35:59 debian kernel: [ 3282.503589] gdm-x-session	1292	1292
Nov 16 21:35:59 debian kernel: [ 3282.503590] Xorg	1294	1294
Nov 16 21:35:59 debian kernel: [ 3282.503590] dbus-daemon	1301	1301
Nov 16 21:35:59 debian kernel: [ 3282.503591] gnome-session-b	1304	1304
Nov 16 21:35:59 debian kernel: [ 3282.503591] ssh-agent	1354	1354
Nov 16 21:35:59 debian kernel: [ 3282.503592] at-spi-bus-laun	1363	1363
Nov 16 21:35:59 debian kernel: [ 3282.503592] dbus-daemon	1368	1368
Nov 16 21:35:59 debian kernel: [ 3282.503593] at-spi2-registr	1371	1371
Nov 16 21:35:59 debian kernel: [ 3282.503593] gnome-shell	1389	1389
Nov 16 21:35:59 debian kernel: [ 3282.503593] gvfsd	1394	1394
Nov 16 21:35:59 debian kernel: [ 3282.503594] gvfsd-fuse	1399	1399
Nov 16 21:35:59 debian kernel: [ 3282.503594] pulseaudio	1412	1412
Nov 16 21:35:59 debian kernel: [ 3282.503595] gnome-shell-cal	1418	1418
Nov 16 21:35:59 debian kernel: [ 3282.503595] evolution-sourc	1422	1422
Nov 16 21:35:59 debian kernel: [ 3282.503596] goa-daemon	1432	1432
Nov 16 21:35:59 debian kernel: [ 3282.503596] mission-control	1443	1443
Nov 16 21:35:59 debian kernel: [ 3282.503597] goa-identity-se	1451	1451
Nov 16 21:35:59 debian kernel: [ 3282.503597] gvfs-udisks2-vo	1452	1452
Nov 16 21:35:59 debian kernel: [ 3282.503598] udisksd	1459	1459
Nov 16 21:35:59 debian kernel: [ 3282.503598] gvfs-goa-volume	1466	1466
Nov 16 21:35:59 debian kernel: [ 3282.503598] gvfs-gphoto2-vo	1470	1470
Nov 16 21:35:59 debian kernel: [ 3282.503599] gvfs-afc-volume	1474	1474
Nov 16 21:35:59 debian kernel: [ 3282.503599] gvfs-mtp-volume	1479	1479
Nov 16 21:35:59 debian kernel: [ 3282.503600] gnome-settings-	1485	1485
Nov 16 21:35:59 debian kernel: [ 3282.503600] evolution-calen	1500	1500
Nov 16 21:35:59 debian kernel: [ 3282.503601] gnome-software	1509	1509
Nov 16 21:35:59 debian kernel: [ 3282.503601] tracker-miner-f	1510	1510
Nov 16 21:35:59 debian kernel: [ 3282.503601] evolution-calen	1514	1514
Nov 16 21:35:59 debian kernel: [ 3282.503602] gsd-printer	1524	1524
Nov 16 21:35:59 debian kernel: [ 3282.503602] vmtoolsd	1531	1531
Nov 16 21:35:59 debian kernel: [ 3282.503603] evolution-alarm	1532	1532
Nov 16 21:35:59 debian kernel: [ 3282.503603] tracker-store	1533	1533
Nov 16 21:35:59 debian kernel: [ 3282.503604] tracker-extract	1534	1534
Nov 16 21:35:59 debian kernel: [ 3282.503604] tracker-miner-u	1541	1541
Nov 16 21:35:59 debian kernel: [ 3282.503605] tracker-miner-a	1547	1547
Nov 16 21:35:59 debian kernel: [ 3282.503605] dconf-service	1563	1563
Nov 16 21:35:59 debian kernel: [ 3282.503606] evolution-calen	1568	1568
Nov 16 21:35:59 debian kernel: [ 3282.503606] evolution-addre	1569	1569
Nov 16 21:35:59 debian kernel: [ 3282.503607] evolution-addre	1588	1588
Nov 16 21:35:59 debian kernel: [ 3282.503607] gvfsd-trash	1662	1662
Nov 16 21:35:59 debian kernel: [ 3282.503607] gvfsd-burn	1676	1676
Nov 16 21:35:59 debian kernel: [ 3282.503608] gnome-terminal-	1687	1687
Nov 16 21:35:59 debian kernel: [ 3282.503608] bash	1693	1693
Nov 16 21:35:59 debian kernel: [ 3282.503609] su	1714	1714
Nov 16 21:35:59 debian kernel: [ 3282.503609] systemd	1715	1715
Nov 16 21:35:59 debian kernel: [ 3282.503610] (sd-pam)	1716	1716
Nov 16 21:35:59 debian kernel: [ 3282.503610] bash	1720	1720
Nov 16 21:35:59 debian kernel: [ 3282.503610] vmhgfs-fuse	1726	1726
Nov 16 21:35:59 debian kernel: [ 3282.503611] bash	1760	1760
Nov 16 21:35:59 debian kernel: [ 3282.503611] bash	1793	1793
Nov 16 21:35:59 debian kernel: [ 3282.503612] systemd-network	1833	1833
Nov 16 21:35:59 debian kernel: [ 3282.503612] bash	2130	2130
Nov 16 21:35:59 debian kernel: [ 3282.503613] gvfsd-metadata	2137	2137
Nov 16 21:35:59 debian kernel: [ 3282.503613] kworker/u4:0	2237	2237
Nov 16 21:35:59 debian kernel: [ 3282.503614] kworker/0:2	3274	3274
Nov 16 21:35:59 debian kernel: [ 3282.503614] dhclient	3341	3341
Nov 16 21:35:59 debian kernel: [ 3282.503615] bash	3526	3526
Nov 16 21:35:59 debian kernel: [ 3282.503615] kworker/u4:1	3698	3698
Nov 16 21:35:59 debian kernel: [ 3282.503615] kworker/0:0	5970	5970
Nov 16 21:35:59 debian kernel: [ 3282.503616] firefox-esr	6588	6588
Nov 16 21:35:59 debian kernel: [ 3282.503616] Privileged Cont	6631	6631
Nov 16 21:35:59 debian kernel: [ 3282.503617] WebExtensions	6679	6679
Nov 16 21:35:59 debian kernel: [ 3282.503617] Web Content	6716	6716
Nov 16 21:35:59 debian kernel: [ 3282.503618] Web Content	6742	6742
Nov 16 21:35:59 debian kernel: [ 3282.503618] sd_generic	6772	6772
Nov 16 21:35:59 debian kernel: [ 3282.503618] sd_espeak-ng	6775	6775
Nov 16 21:35:59 debian kernel: [ 3282.503619] sd_dummy	6783	6783
Nov 16 21:35:59 debian kernel: [ 3282.503619] speech-dispatch	6786	6786
Nov 16 21:35:59 debian kernel: [ 3282.503620] kdeinit5	6952	6952
Nov 16 21:35:59 debian kernel: [ 3282.503620] klauncher	6953	6953
Nov 16 21:35:59 debian kernel: [ 3282.503621] kdevelop	7036	7036
Nov 16 21:35:59 debian kernel: [ 3282.503621] kworker/1:0	7122	7122
Nov 16 21:35:59 debian kernel: [ 3282.503622] Web Content	7146	7146
Nov 16 21:35:59 debian kernel: [ 3282.503622] kworker/1:1	7178	7178
Nov 16 21:35:59 debian kernel: [ 3282.503623] kworker/0:1	7220	7220
Nov 16 21:35:59 debian kernel: [ 3282.503623] file.so	7239	7239
Nov 16 21:35:59 debian kernel: [ 3282.503623] kworker/1:2	7240	7240
Nov 16 21:35:59 debian kernel: [ 3282.503624] bash	7259	7259
Nov 16 21:35:59 debian kernel: [ 3282.503624] kworker/u4:2	7784	7784
Nov 16 21:35:59 debian kernel: [ 3282.503624] sudo	8339	8339
Nov 16 21:35:59 debian kernel: [ 3282.503625] insmod	8340	8340
```

(c) Which parts of the kernel module execute in kernel space?  
