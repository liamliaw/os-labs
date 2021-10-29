# Lab 1

## Exercise 1.

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