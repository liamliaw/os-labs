# Lab 1

## Exercise 1

*(a) Use the lsb_release command to print all the distribution details.*  
I first consult the man page of lsb_release and find that the **-a** switch is needed to
print all the distribution details.

```console
moritzpfeffer@debian:~$ man lsb_release
Lots of text
```

Then i use the switch to get all distribution information.

```console
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
