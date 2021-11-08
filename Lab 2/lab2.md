# Lab 2

**Members: Fangwen Liao (3439869), Yijin Wang (3476217), Moritz Pfeffer (3261671)**

## Exercise 1

*(a) Use only the ps command to list all processes (including UID) with parent process ID (PPID) 1.*  
I consult the man page of ps and find the **--ppid** switch.
> --ppid pidlist  
> Select by parent process ID.  This selects the processes with a parent process ID in pidlist.

output format control      u      Display user-oriented format.
output modifier       n      Numeric output for WCHAN and USER (including all types of UID and GID).

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

> --user userlist  
> Select by effective user ID (EUID) or name.  Identical to -u and U.
Like above nu and pipe it into grep where we only include lines without brackets.

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

```console
moritzpfeffer@debian:~$ ps --user root nu | grep -v ] | wc -l
23
```

*(c) For each of the remaining (~22) processes, provide a 2-3 sentences describing the functionality of each process (Hint: use man / Arch)*

*(b) Use the uname command to print the kernel release info.*  
In man page of *uname* the **-r** option is for printing the kernel release info.

```console
moritzpfeffer@debian:~$ man uname
Lots of text
```

```console
moritzpfeffer@debian:~$ uname -r
4.9.0-16-686
```

## Exercise 2

*(a) Determine the shell that is used by default by using the echo command and
the $SHELL environment variable.*  
The value of an environment variable can be simply accessed by command *echo*.
<div style="page-break-after: always;"></div>

```console
moritzpfeffer@debian:~$ ps --user root nu | grep -v ] | wc -l
23
```

This shows that the default shell is *bash* located at */bin/bash*.

*(b) List all the directories found in the $PATH environment variable.*

```console
moritzpfeffer@debian:~$ echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
```

Thus, the directories in $PATH are:

* /usr/local/bin
* /usr/bin
* /bin
* /usr/local/games
* /usr/games

## Exercise 3

*List the number of scripts that run  
(a) at run level S, (b) run level 2, and (c) run level 5.*  
In the man page of *wc* the **-l** option shows the number of lines of data fed to the command.\
To count the files in a directory, pipe *ls* with *wc -l*.\
Scripts of each run level [can be found in */etc/rc.x* files](https://www.geeksforgeeks.org/run-levels-linux/).

```console
moritzpfeffer@debian:/etc$ ls /etc/rcS.d/ | wc -l
10
moritzpfeffer@debian:/etc$ ls /etc/rc2.d/ | wc -l
20
moritzpfeffer@debian:/etc$ ls /etc/rc5.d/ | wc -l
20
```

Therefore, at run level S **10** scripts are running, at run level 2 **20** scripts and at level 5 **20** scripts.

## Exercise 4

*Research the difference between systemd and init (System V init)  
(a) describe in your own words the difference between these systems*  
I will describe five differences between systemd and init.  
One important motivation for systemd was to speed up boot times. To achieve this systemd starts services **on demand** and in **parallel**, while init starts services **serially**. [[1]](http://0pointer.de/blog/projects/systemd.html)  
Secondly init starts services through shell script, while systemd recommend .service files. Thus, I would say that init uses an **imperative** approach (scripts), whereas systemd prefers a **declarative** one. [[2]](https://danielmiessler.com/study/the-difference-between-system-v-and-systemd/)  
Thirdly, systemd’s .service files and other unit files can be grouped into **targets**, which **replace init’s runlevels**.  
Furthermore, systemd contains many more components than init. For example, it features a ntp-implementation called systemd-timesyncd and systemd-timer which can run recurring tasks like cron does.
Thus, the fourth difference is that **systemd is less focused and larger** than the original init. [[3]](https://lwn.net/Articles/804989/)  
Fifthly, as for SystemV, all of these programs are open and understandable scripts, while systemd is a complex system of large compiled binary executables that are not understandable without access to the source code. Although it’s open source, it is just less convenient. [[4]](https://link.springer.com/chapter/10.1007/978-1-4842-5455-4_13)

*(b) determine which of the two is used by the operating system you have installed in VirtualBox. How
can you tell?*  
The [archlinux wiki on systemd](https://wiki.archlinux.org/title/Systemd) tells us that systemctl is the "main command used to introspect and control systemd is systemctl".
By entering "systemctl" into the terminal and executing it i confirm that it is present in the VM. From that i infer that systemd is used.

Additionally, systemd or init will run as the first process in operating system and according to man page, the PID should be 1.

```console
fangwenliao@debian:~$ ps -e | less -X
  PID TTY          TIME CMD
    1 ?        00:00:01 systemd
```

or

```console
fangwenliao@debian:~$ ps -ef | less -X
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 21:22 ?        00:00:01 /sbin/init
```

```console
fangwenliao@debian:/sbin$ ls -l init
lrwxrwxrwx 1 root root 20 Jul  8 15:07 init -> /lib/systemd/systemd
```
