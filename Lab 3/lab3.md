# Lab 3

**Members: Fangwen Liao (3439869) | Yijin Wang (3476217)**

## Exercise 1
*Inspect the nice command (man nice)*
*(a)use the nicecommand to create a process that executes ping localhost with a nice value of 19.*
According to man page of nice, it is used to modify scheduling priority.
Run the command nice to assign the value 19 to the ping command.
```console
fangwenliao@debian:~$ nice -19 ping localhost
PING localhost(localhost (::1)) 56 data bytes
64 bytes from localhost (::1): icmp_seq=1 ttl=64 time=0.053 ms
64 bytes from localhost (::1): icmp_seq=2 ttl=64 time=0.055 ms
64 bytes from localhost (::1): icmp_seq=3 ttl=64 time=0.065 ms
64 bytes from localhost (::1): icmp_seq=4 ttl=64 time=0.073 ms
64 bytes from localhost (::1): icmp_seq=5 ttl=64 time=0.095 ms
64 bytes from localhost (::1): icmp_seq=6 ttl=64 time=0.073 ms
64 bytes from localhost (::1): icmp_seq=7 ttl=64 time=0.070 ms
64 bytes from localhost (::1): icmp_seq=8 ttl=64 time=0.072 ms
64 bytes from localhost (::1): icmp_seq=9 ttl=64 time=0.074 ms
64 bytes from localhost (::1): icmp_seq=10 ttl=64 time=0.070 ms
64 bytes from localhost (::1): icmp_seq=11 ttl=64 time=0.075 ms
64 bytes from localhost (::1): icmp_seq=12 ttl=64 time=0.074 ms
```

*(b)show that the nice value has changed by inspecting it using the ps command.*
Use ps command to find the pid of ping, then in the output format use the -nice option to show the niceness.
```console
fangwenliao@debian:~$ ps -eo pid,nice,cmd | grep ping
 1007  19 ping localhost
```

## Exercise 2
*Open the loopdirectory and inspect the files. Compile the source file into a binary using the makecommand. The loopbinary takes one integer (nice value) as command line argument. Run the loop process with nice value 0*
*(a) Show the output of the lscpu command, and the cat /proc/sys/kernel/sched_autogroup_enabled command.*
The autogroup feature according to the man page of sched is to prevent some groups of process hogging too much CPU-cycles. So we first set the *sched_autogroup_enabled* value to 0 to disable this feature.
First we need root to set this value. The lscpu command shows that there is only one CPU as required. The autogroup feature is disabled.
```console
fangwenliao@debian:~/Downloads/OS_Lab3/loop$ su
Password: 
root@debian:/home/fangwenliao/Downloads/OS_Lab3/loop# echo 0 > /proc/sys/kernel/sched_autogroup_enabled 
root@debian:/home/fangwenliao/Downloads/OS_Lab3/loop# exit
exit
fangwenliao@debian:~/Downloads/OS_Lab3/loop$ Process ID: 1039
Requested nice: 0
Original nice: 0
New nice: 0

Use CTRL+C (SIGINT) to exit


fangwenliao@debian:~/Downloads/OS_Lab3/loop$ lscpu
Architecture:          i686
CPU op-mode(s):        32-bit
Byte Order:            Little Endian
CPU(s):                1
On-line CPU(s) list:   0
Thread(s) per core:    1
Core(s) per socket:    1
Socket(s):             1
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 142
Model name:            Intel(R) Core(TM) i7-8550U CPU @ 1.80GHz
Stepping:              10
CPU MHz:               1991.999
BogoMIPS:              3983.99
Hypervisor vendor:     KVM
Virtualization type:   full
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              8192K
Flags:                 fpu vme de pse tsc msr mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht rdtscp constant_tsc xtopology nonstop_tsc pni pclmulqdq monitor ssse3 cx16 sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx rdrand hypervisor lahf_lm abm 3dnowprefetch fsgsbase avx2 invpcid rdseed clflushopt md_clear flush_l1d
fangwenliao@debian:~/Downloads/OS_Lab3/loop$ cat /proc/sys/kernel/sched_autogroup_enabled 
0
```
*(b)inspect the current scheduling class of the loopprocess using the chrtcommand. What is the current scheduling class?*
According to man page of chrt, just use -p option to retreive real-time attributes of a task. According to the man page of sched, the SCHED_OTHER means that this process use the default linux time sharing scheduling, and the priority is can only be 0, however a dynamic nice value will be generated each time quantum to decide when to run.
```console
fangwenliao@debian:~/Downloads/OS_Lab3/loop$ chrt -p 1039
pid 1039's current scheduling policy: SCHED_OTHER
pid 1039's current scheduling priority: 0
```
(c) Using the same command, change the scheduling class to realtime, what happens to the system’s interactiveness(of the GUI for example), and why?
First check the priority range using chrt --max. According to man page of sched, there are two real-time scheduling policies in linux: FIFO and RR, both will preempt any other non real time tasks, only that by RR a task runs in a time slice, which is less aggresive than FIFO.
A root access is needed to change the real time scheduling policy. After changing it, the terminal react much slower than usual(GUI is not started on our machine).
```console
fangwenliao@debian:~/Downloads/OS_Lab3/loop$ chrt --max
SCHED_OTHER min/max priority	: 0/0
SCHED_FIFO min/max priority	: 1/99
SCHED_RR min/max priority	: 1/99
SCHED_BATCH min/max priority	: 0/0
SCHED_IDLE min/max priority	: 0/0
SCHED_DEADLINE min/max priority	: 0/0
fangwenliao@debian:~/Downloads/OS_Lab3/loop$ su
Password: 
root@debian:/home/fangwenliao/Downloads/OS_Lab3/loop# chrt -f -p 99 1039
root@debian:/home/fangwenliao/Downloads/OS_Lab3/loop# chrt -r -p 99 1039

```
In order to look deep into what is happing, a new ssh window is opened and check the interrupts of the system. When doing nothing, the only increasing(also regularly) interrupts are caused by enp0s3 which is ethernet perepherial, Local timer interrupts and  ata_piix which is Intel PATA/SATA controllers according to the ata_piix.c, and without running loop, they increase just about the same rate. 
The only noticable interrupt that cause lagging is caused by i8042, which according to i8042.c is the keyboard and mouse controller for linux. Each time of a type on our keyboard will cause two(press and release) interrupts, and a obvious lagging in terminal. So it is resonable to guess that the I/O process of keyboard input,  which is also a real-time process are preempted by loop.
 
```console
fangwenliao@debian:~/Downloads/OS_Lab3/loop$ watch -n.1 "cat /proc/interrupts"
Every 0.1s: cat /proc/interrupts####################debian: Sun Nov 28 22:59:22 2021

###########CPU0
##0:#########40   IO-APIC   2-edge######timer
##1:########640   IO-APIC   1-edge######i8042
##8:##########0   IO-APIC   8-edge######rtc0
##9:#########50   IO-APIC   9-fasteoi   acpi
 12:########162   IO-APIC  12-edge######i8042
 14:##########0   IO-APIC  14-edge######ata_piix
 15:#######7627   IO-APIC  15-edge######ata_piix
 18:##########0   IO-APIC  18-fasteoi   vmwgfx
 19:######20212   IO-APIC  19-fasteoi   enp0s3
 20:#######3081   IO-APIC  20-fasteoi   vboxguest
 21:#######9384   IO-APIC  21-fasteoi   ahci[0000:00:0d.0], snd_intel8x0
 22:#########26   IO-APIC  22-fasteoi   ohci_hcd:usb1
NMI:##########0   Non-maskable interrupts
LOC:    2280934   Local timer interrupts
SPU:##########0   Spurious interrupts
PMI:##########0   Performance monitoring interrupts
IWI:##########0   IRQ work interrupts
RTR:##########0   APIC ICR read retries
RES:##########0   Rescheduling interrupts
CAL:##########0   Function call interrupts
TLB:##########0   TLB shootdowns
TRM:##########0   Thermal event interrupts

```



##Exercise 3
*LecturesExercises 2/23.For this exercise make sure that there are at most two loop processes running, and as few as possible foreground/background processes*
*(a) Start the first loopprocess with nice value 0, use the ampersand (&) to execute it in the background*
*(b) Now start another loopprocess in the same terminal with nice value 0 and use the timecommand to measure real/user/system time of this new process, after +/-30 seconds terminate the process with CTRL+C. Show and explain the difference between real/user time, explainthe ratio between these numbers*
According to the const int sched_prio_to_weight[40] in kernel/sched/core.c, the ratio can be calculated in a very simple fomular: r=weight2/(weight1+weight2). The theoretical ratio should be 0.5, and in our test it is 0.4993.
```console
fangwenliao@debian:~/Downloads/OS_Lab3/loop$ Process ID: 1045
Requested nice: 0
Original nice: 0
New nice: 0

Use CTRL+C (SIGINT) to exit


fangwenliao@debian:~/Downloads/OS_Lab3/loop$ time ./loop 0
Process ID: 1047
Requested nice: 0
Original nice: 0
New nice: 0

Use CTRL+C (SIGINT) to exit

^C

real	0m29.528s
user	0m14.744s
sys	0m0.000s
```
*(c) Repeat step (a) four times, using the following nice values for the second process 19, 10, -10, and -20. Show and explain the difference between real/user time, explain also the ratiobetween these numbers using the CFS Example slide (slide 14)*
With the lower nice value, the priority of a process is higher. Root is needed when assigning negative values. Theoretical ratio of value 19, 10, -10, -20 is respectively 15/(15+1024)=1.44%, 110/(110+1024)=10.74%, 9548/(9548+1024)=90.31%, 88761/(88761+1024)98.86%, and our test result is 1.43%, 9.49%, 90.08%, 98.66%. 
```console
fangwenliao@debian:~/Downloads/OS_Lab3/loop$ ./loop 0 &
[1] 1058
fangwenliao@debian:~/Downloads/OS_Lab3/loop$ Process ID: 1058
Requested nice: 0
Original nice: 0
New nice: 0

Use CTRL+C (SIGINT) to exit


fangwenliao@debian:~/Downloads/OS_Lab3/loop$ time ./loop 19
Process ID: 1059
Requested nice: 19
Original nice: 0
New nice: 19

Use CTRL+C (SIGINT) to exit

^C

real	0m30.858s
user	0m0.444s
sys	0m0.000s
fangwenliao@debian:~/Downloads/OS_Lab3/loop$ time ./loop 10
Process ID: 1062
Requested nice: 10
Original nice: 0
New nice: 10

Use CTRL+C (SIGINT) to exit

^C

real	0m30.169s
user	0m2.920s
sys	0m0.000s
fangwenliao@debian:~/Downloads/OS_Lab3/loop$ su
Password: 
root@debian:/home/fangwenliao/Downloads/OS_Lab3/loop# time ./loop -10
Process ID: 1082
Requested nice: -10
Original nice: 0
New nice: -10

Use CTRL+C (SIGINT) to exit

^C

real	0m30.795s
user	0m27.740s
sys	0m0.028s
root@debian:/home/fangwenliao/Downloads/OS_Lab3/loop# time ./loop -20
Process ID: 1086
Requested nice: -20
Original nice: 0
New nice: -20

Use CTRL+C (SIGINT) to exit

^C

real	0m30.197s
user	0m29.792s
sys	0m0.012s
```

*(d) in loop.c, add a usleep(1)statement in the while loop, build the new binary, and use the timecommand to inspect the real/user/system time when running loopwith nice -20, show and explain the differences with/without usleep*

```console
fangwenliao@debian:~/Downloads/OS_Lab3/loop$ su
Password: 
root@debian:/home/fangwenliao/Downloads/OS_Lab3/loop# time ./loop -20
Process ID: 1146
Requested nice: -20
Original nice: 0
New nice: -20

Use CTRL+C (SIGINT) to exit

^C

real	0m30.373s
user	0m6.492s
sys	0m0.000s
```

##Exercise 4
*To monitor the context switches of a process, use watch –n.1 grep ctxt/proc/pid/statuswhere pidshould be replaced with an actual process ID. Monitor the context switches (for about 30 seconds) of the original loopprocess without the usleep, and the modified loopprocess with the usleep, both with a nice value of 0. Show and explain the number of voluntary and involuntary context switches in both cases*


Thus, my command appears to be correct.  

*(c) For each of the remaining (~22) processes, provide a 2-3 sentences describing the functionality of each process (Hint: use man / Arch)*  

| Program Name                             | Description |
| :--------------------------------------- | :---------- |
| /sbin/init                               | This is the first running process with PID 1. On our system it links to /lib/systemd/systemd. It is responsible for bringing up services, running init scripts, mounting filesystems. Systemd was created by Lennart Poettering and replaces the SystemV init as discussed in exercise 1. *(man + Arch)* |
| /lib/systemd/systemd-journald            | systemd-journald is a part of systemd and a service that aggregates logging message from various sources e.g. kernel logs. Logging messages are classified by their source and priority. *(man + Arch)* |
| /sbin/lvmetad                         | lvmetad serves as cache for LVM metadata. LVM stands for logical volume manager and is a storage abstraction layer. *(man)*   |
| /lib/systemd/systemd-udevd               |  [Wikipedia](https://en.wikipedia.org/wiki/Udev#Operation) says that udev stands for "userspace /dev" and that it is a device manager. The kernel emits a uevent for example when a usb device is plugged in. Systemd-udevd itself runs in userspace but listens to these events. Then it creates or removes corresponding entries in /dev [[1]](https://www.linux-community.de/ausgaben/linuxuser/2019/05/unsichtbarer-helfer/) |
| /usr/sbin/irqbalance                     | irqbalance distributes "interrupts accross processors on a multiprocessor system". It allows the user to customize the handling of interrupts by providing a policy script targeted at a specific IRQ. *(man)*|
| /lib/systemd/systemd-logind              | systemd-logind is a service that handles user authentication. According to man, this includes "keeping track of users and sessions", session management and "device access management for users".           |
| /usr/sbin/NetworkManager                 | NetworkManger is a service with the goal of simplyfying networking. It can manage multiple connection e.g. (Wifi + 3G) at once. Also it has the default policy to connect to networks whenever available. *(man)*|
| /usr/lib/accountsservice/accounts-daemon | The accounts-daemon resides in the /usr/lib/accountsservice directory which shows that it is part of the [AccountsService by freedesktop](https://www.freedesktop.org/wiki/Software/AccountsService/). It exposes a "D-Bus interface for querying and manipulating user account information" (*man*). This makes for a more convenient API as developers don't have to fork of programs such as adduser [[2]](https://news.ycombinator.com/item?id=25054695).|
| /usr/sbin/rsyslogd                       | It provides support for message logging while avoiding auto-backgrounding. |
| /usr/sbin/ModemManager                   |  provides a unified high level API for communicating with mobile broadband modems, regardless of the protocol used to communicate with the actual device (Generic AT, vendor-specific AT, QCDM, QMI, MBIM...).            |
| /usr/lib/policykit-1/polkitd             | polkitd provides the org.freedesktop.PolicyKit1 D-Bus service on the system message bus. Users or administrators should never need to start this daemon as it will be automatically started by dbus-daemon(1) whenever an application calls into the service.            |
| /usr/sbin/sshd                           | Provides secure encrypted communications between two untrusted hosts over an insecure network. When this option is specified, sshd will not detach and does not become a daemon.  This allows easy monitoring of sshd.            |
| /usr/sbin/gdm3                           | gdm3 reads /etc/gdm3/custom.conf for its configuration. For each local display, gdm starts an X server and runs a minimal GNOME session including a graphical greeter. If configured so, the main gdm process also listens for XDMCP requests from remote displays.            |
| /usr/sbin/VBoxService                    | Enables interoperability between host and the VM such as copy paste, shared folders etc. Also writes the process ID of the service to a file in /var/run/vboxadd-service.sh.            |
| /usr/sbin/minissdpd                      |  It listens for SSDP traffic and keeps track of what are the UPnP devices up  on  the network while the name  or  IP address of the interface used to listen to SSDP packets coming on multicast address 0.0.0.0.            |
| /usr/lib/upower/upowerd                  |UPower gives an interface of power source energy management and [upowerd](https://manpages.debian.org/stretch/upower/upowerd.8) provides this service on the system bus. it automatically starts.             |
| /sbin/wpa_supplicant                     |it is a backend for wireless network interface configuration, it starts when the wireless interface is raised. [How to use a WiFi interface](https://wiki.debian.org/WiFi/HowToUse#wpa_supplicant)             |
| /usr/lib/packagekit/packagekitd          |[PackageKit](https://wiki.debian.org/PackageKit) makes common software task easier and smoother and is distribution neutral.            |
| /usr/sbin/cron                           |it is responsible for the [time based job scheduling](https://wiki.debian.org/cron) in UNIX, it is a very old programm and receives little maintance by debian.             |
| /sbin/dhclient                           |it is used go configure the [DHCP client](https://wiki.debian.org/DHCP_Server)(Dynamic Host Configuration Protocol automatically sets up LAN) when setting up a LAN.             |
| /usr/lib/udisks2/udisksd                 |[udisks](https://manpages.debian.org/testing/udisks2/udisks.8.en.html) is used to operate disks or storage devices and udisksd is its daemon that starts automatically.             |
| /usr/sbin/cupsd                          |[CUPS](https://manpages.debian.org/testing/cups/cups.1.en.html) prints from user applications, it converts informations for the printer to understand, and [cupsd](https://manpages.debian.org/testing/cups-daemon/cupsd.8.en.html) is its scheduler.             |

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
According to the man page of fork, the fork() copies the calling process in a separate memory space and the child process has the same content.
In the source code there are 3 fork() calls. When process 1845 fork for the first time, it creates 1846, at this point both 1845 and 1846 will run the second fork.
In the second fork, 1845 and 1846 create correspondly 1849 and 1847, after that 1845, 1846, 1847 1849 are prepared for the third fork.
Finally 1852, 1851, 1850 1848 are forked.  
*(c) Do these processes share memory or other resources? Why (not)?*  
According to man page of fork, they are in different memory space, however fork() in Linux uses copy-on-write technique, they actually share the same physical memory, because they didn't write anything.

<div style="page-break-after: always;"></div>

## Exercise 3

*(a) Implement an application in C that uses (1) a clone() system call to create a process, (2) a clone() system call to create a thread, and (3) a fork() (in that specific order)*  
For creating a process using clone, i straced a program that forks to learn the arguments to clone.  
For creating a thread using clone, i consulted the man pages of clone.

```c
#include <linux/sched.h> // CLONE flags
#include <signal.h>      // SIGCHLD flag
#include <stdio.h>       // printf()
#include <stdlib.h>      // malloc
#include <unistd.h>      // sleep(), getpid(), getppid()

void printids() {
    printf("TGID: %d\n", getpid());  // Print the TGID of the current process
    printf("PPID: %d\n", getppid()); // Print the PPID of the current process
}

int main(void) {
    pid_t pid;
    printf("[Parent Process]\n");
    printids(); // IDs of the main process
    printf("[Clone Process]\n");
    void *child_stack;
    child_stack = (void *)malloc(8192);
    clone(&printids, child_stack + 8192, CLONE_CHILD_CLEARTID | CLONE_CHILD_SETTID | SIGCHLD);
    wait();
    free(child_stack);
    printf("[Clone Thread]\n");
    void *child_stack2;
    child_stack2 = (void *)malloc(8192);
    clone(&printids, child_stack2 + 8192, CLONE_VM | CLONE_FS | CLONE_FILES | CLONE_SIGHAND | CLONE_THREAD, 0);
    sleep(1);
    if (fork() == 0) {
        printf("[Fork Process]\n");
        printids();
    } else {
        wait();
        free(child_stack2);
    }
    return 0;
}
```

*(b) For each of the created processes and threads, print and clearly show the TGID and PPID.*

```console
moritzpfeffer@debian:~$ ./a.out 
[Parent Process]
TGID: 16846
PPID: 16073
[Clone Process]
TGID: 16847
PPID: 16846
[Clone Thread]
TGID: 16846
PPID: 16073
[Fork Process]
TGID: 16850
PPID: 16846
```

*(c) Explain why each of the TGIDs and PPIDs have the values as shown.*  
Lets look at all PPIDs first.  
We have [Parent Process].PID = [Parent Process].TGID = [Clone Process].PPID = [Fork Process].PPID.  
That is because [Parent Process] spawns the other two processes and is their parent.  
In contrast, [Parent Process].PID != [Clone Thread].PPID.  
But, [Parent Process].PPID == [Clone Thread].PPID because both have the same parent which is the shell.

Now lets look at TGIDs.  
[Parent Process], [Clone Process] and [Fork Process] each have their own TGID which also serves as their PID because
they are distinct processes.  
Only [Parent Process].TGID = [Clone Thread].TGID because both threads run in the same process.

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

*(c) Which parts of the kernel module execute in kernel space?*  
All parts of the kernel module execute in kernel space. This can be seen from the fact that the kernel function
*printk* is available in all the procedures i.e. init, exit and list_processes.
