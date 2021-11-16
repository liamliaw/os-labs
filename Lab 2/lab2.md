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
Answer.

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

*(a)Run the binary and show the process tree using pstreeand the parent PID.*
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
*(b)Explain the number of running processes based on the source code in fork.c.*
According to man page of fork, the fork() copies the calling process in a seperate memory space and the child process has the same content.
In the source code there are 3 fork() calls. When process 1845 fork for the first time, it creates 1846, at this point both 1845 and 1846 will run the second fork.
In the second fork, 1845 and 1846 create correspondly 1849 and 1847, after that 1845, 1846, 1847 1849 are prepared for the third fork.
Finally 1852, 1851, 1850 1848 are forked. 
*(c)Do these processes share memory or other resources? Why (not)?*
Arrording to man page of fork, they are in different memory space, however fork() in Linux use copy-to-write technique, they actually share the same physical memory, because they didn't write anything. 


## Exercise 3
*Open the clone directory.* 

*(a) Implement an application in C that uses (1) a clone() system call to create a process, (2) a clone() system call to create a thread, and (3) a fork() (in that specific order)*
```console
#include <stdio.h> //printf()
#include <unistd.h> //sleep(), getpid(), getppid()
#include <signal.h> //SIGCHLD flag
#include <linux/sched.h> //CLONE flag

void printids(void){
    printf("TGID: %d\n", getpid());    
    printf("PPID: %d\n", getppid());
    printf("\n\n");
    sleep(1);
}

int child(void *arg)
{
    printids();
}

int main(void)
{
    printids();

//use a clone to create a process
    void *child_stack;
    child_stack = (void *)malloc(1000);
    if(child_stack == NULL)
    {
   	 exit(0);    
    }
    printf("This is a child process created by clone ()\n");
    clone(child, child_stack+1000, SIGCHLD, 0);
    sleep(1);

//use a clone to create a thread
    printf("This is a thread created by clone()\n");
    clone(child, child_stack+2000, CLONE_SIGHAND|CLONE_FS|CLONE_VM|CLONE_FILES, 0);
    sleep(1);


//use a fork
    pid_t pid;
    pid = fork();
    if(pid == 0)
    {
   	 printf("This is a child process created by fork()\n");
   	 printids();
   	 exit(0);
    }
    else if (pid > 0)
    {
    wait(NULL);
    }
    sleep(1);
    
    free(child_stack);

    return 0;

}

```
*(b) For each of the created processes and threads, print and clearly show the TGID and PPID.*
```console
yijinwang@debian:~$ cd /home/yijinwang/Documents
yijinwang@debian:~/Documents$ gcc -o lab2 lab2.c

yijinwang@debian:~/Documents$ ./lab2
TGID: 3462
PPID: 3443


This is a child process created by clone ()
TGID: 3463
PPID: 3462


This is a thread created by clone()
TGID: 3464
PPID: 3462


This is a child process created by fork()
TGID: 3466
PPID: 3462

```
*(c) Explain why each of the TGIDs and PPIDs have the values as shown.*

The TGID of the first one is also used as process ID. The other three processes are all created by the first process, so they share the same PPID which is the PID of the first one.
The three child processes fully copy the parent process, each process is guaranteed a unique TGID used to identify.

## Exercise 4
