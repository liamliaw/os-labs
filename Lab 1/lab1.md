# Lab 1

**Members: Fangwen Liao (3439869), Yijin Wang (3476217), Moritz Pfeffer (3261671)**

## Exercise 1

*(a) Use the lsb_release command to print all the distribution details.*  
I first consult the man page of lsb_release and find that the **-a** switch is needed to
print all the distribution details.

```bash
moritzpfeffer@debian:~$ man lsb_release
Lots of text
```

Then i use the switch to get all distribution information.

```shell
moritzpfeffer@debian:~$ lsb_release -a
No LSB modules are available.
Distributor ID: Debian
Description: Debian GNU/Linux 9.13 (stretch)
Release: 9.13
Codename: stretch
```

*(b) Use the uname command to print the kernel release info.*  
I first consult the man page of uname and find that the **-r** switch is needed to
print the kernel release info

```console
moritzpfeffer@debian:~$ man uname
Lots of text
```

Then i use the switch to get all distribution information.

```console
moritzpfeffer@debian:~$ uname -r
4.9.0-16-686
```

## Exercise 2

*(a) Determine the shell that is used by default by using the echo command and
the $SHELL environment variable.*  
To get the value of an environment variable we can simply echo it.
<div style="page-break-after: always;"></div>

```console
moritzpfeffer@debian:~$ echo $SHELL
/bin/bash
```

This shows that the default shell is *bash* located at */bin/bash*.

*(b) List all the directories found in the $PATH environment variable.*

```console
moritzpfeffer@debian:~$ echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
```

Thus the directories in $PATH are:

* /usr/local/bin
* /usr/bin
* /bin
* /usr/local/games
* /usr/games

## Exercise 3

*List the number of scripts that run  
(a) at run level S, (b) run level 2, and (c) run level 5.*  
Reading the man page of wc i find that the **-l** option reduces the output of wc only to the number of lines fed into it.
Combining it with ls allows me to count the files in a directory.

```console
moritzpfeffer@debian:/etc$ ls /etc/rcS.d/ | wc -l
10
moritzpfeffer@debian:/etc$ ls /etc/rc2.d/ | wc -l
20
moritzpfeffer@debian:/etc$ ls /etc/rc5.d/ | wc -l
20
```

Thus (a) at run level S **10** scripts are run and at (b) run level 2 **20** scripts are run, and at (c) run level 5 **20** scripts are run.

## Exercise 4

*Research the difference between systemd and init (System V init)  
(a) describe in your own words the difference between these systems*  
I will describe four differences between systemd and init.  
One important motivation for systemd was to speed up boot times. To achieve this systemd starts services **on demand** and in **parallel**, while init starts services **serially**. [[1]](http://0pointer.de/blog/projects/systemd.html)  
Secondly init starts services through shell script, while systemd recommend .service files. Thus, I would say that init uses an **imperative** approach (scripts), whereas systemd prefers a **declarative** one. [[2]](https://danielmiessler.com/study/the-difference-between-system-v-and-systemd/)  
Thirdly, systemd’s .service files and other unit files can be grouped into **targets**, which **replace init’s runlevels**.  
Furthermore, systemd contains many more components than init. For example, it features a ntp-implementation called systemd-timesyncd and systemd-timer which can run recurring tasks like cron does.
Thus, the fourth difference is that **systemd is less focused and larger** than the original init. [[3]](https://lwn.net/Articles/804989/)  
*(b) determine which of the two is used by the operating system you have installed in VirtualBox. How
can you tell?*
The [archlinux wiki on systemd](https://wiki.archlinux.org/title/Systemd) tells us that systemctl is the "main command used to introspect and control systemd is systemctl".
By entering "systemctl" into the terminal and executing it i confirm that it is present in the VM. From that i infer that systemd is used.
