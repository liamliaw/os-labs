# Lab 5

**Members: Fangwen Liao (3439869) | Yijin Wang (3476217)**

## Exercise 1
*The Linux kernel enforces the following policy: a process is not allowed to try to acquire a semaphore while it is already holding a spin lock.*

*(a) Explain why this policy has to be enforced.*

When a process hold a spin lock, it will loop until it gets the resource, it should not wait for a long time. When holding a semaphore, a process can go to sleep, so a process can not go to sleep while holding a spin lock, or when it sleeps it can not release the spin lock.


*(b) What can happen when the policy is not enforced?*
The process holding a spin lock and a semaphore can go to sleep and neverrelease the spin lock, this may causing deadlock, or it should go to  sleep, but spinning and waste cpu cycles.
If a process grabs a spinlock and goes to sleep before releasing it. A second process (or an interrupt handler) that to grab the spinlock will busy wait. On an uniprocessor machine the second process will lock the CPU not allowing the first process to wake up and release the spinlock so the second process can continue, it is basically a deadlock.

## Exercise 2
*Describe the problem that occurs when using spinlocks on a single processor system. Consider both the case of*

*(a) a preemptible kernel and*

When a process holding a spinlock is preempted, it doesn't release the lock, if the preempting process need the resource, it will never get it, thus forming a deadlock.
Spinlock automatically disables preemption, which avoids deadlock caused by interrupts. when data is shared with interrupt handler, before holding spinlock we must disable interrupts.

*(b) a non preemptible kernel.*

In a non-preemptible kernel, a process will release the resource only when it finishes using it and will not causing a dead lock for the next process.

## Exercise 3
*Explain what concurrency issues can arise when the up() (increment counter) and down() (decrement counter) functions of a semaphore are not executed atomically. Illustrate these issues with an example using 2 threads.*

when Thread0 aquire the semaphore, the count should be decreased from 1 to zero, but as down() not atomically runned, the count stays 1, thus Thread1 can also get the resource, thus causing data inconsistancy.
When Thread0 finishes using resrouce and release the semaphore, as up() not excuted atomically, Thread1 will never be able to get the resource.

*Download OS_Lab5.zip and unzip it. Open the kernel directory. It is recommended to make a linked clone of your virtual machine before continuing.*

## Exercise 4
*Inspect the *.sh scripts and download + compile the Linux kernel using these scripts.*

*(a) Show the output of uname a before installation.*

The provided sh files have problems when running, to solve this problem, we
copy them and run them inside the virtual machinei.
```console
fangwenliao@debian:~/Downloads/OS_Lab5/kernel$ uname -a
Linux debian 4.9.0-16-686 #1 SMP Debian 4.9.272-2 (2021-07-19) i686 GNU/Linux
```

*(b) Install the four Debian packages that are created once the compilation of the kernel completes successfully.*

```console
root@debian:/home/yijinwang/Documents/OS_Lab5/kernel# dpkg -i linux-headers-4.19.152_4.19.152-1_i386.deb
Selecting previously unselected package linux-headers-4.19.152.
(Reading database ... 166573 files and directories currently installed.)
Preparing to unpack linux-headers-4.19.152_4.19.152-1_i386.deb ...
Unpacking linux-headers-4.19.152 (4.19.152-1) ...
Setting up linux-headers-4.19.152 (4.19.152-1) ...

```

```console
root@debian:/home/yijinwang/Documents/OS_Lab5/kernel# dpkg -i linux-libc-dev_4.19.152-1_i386.deb
(Reading database ... 190359 files and directories currently installed.)
Preparing to unpack linux-libc-dev_4.19.152-1_i386.deb ...
Unpacking linux-libc-dev (4.19.152-1) over (4.9.290-1) ...
Setting up linux-libc-dev (4.19.152-1) ...
```

```console
root@debian:/home/yijinwang/Documents/OS_Lab5/kernel# dpkg -i linux-image-4.19.152_4.19.152-1_i386.deb
Selecting previously unselected package linux-image-4.19.152.
(Reading database ... 190543 files and directories currently installed.)
Preparing to unpack linux-image-4.19.152_4.19.152-1_i386.deb ...
Unpacking linux-image-4.19.152 (4.19.152-1) ...
Setting up linux-image-4.19.152 (4.19.152-1) ...
update-initramfs: Generating /boot/initrd.img-4.19.152
Generating grub configuration file ...
Found background image: /usr/share/images/desktop-base/desktop-grub.png
Found linux image: /boot/vmlinuz-4.19.152
Found initrd image: /boot/initrd.img-4.19.152
Found linux image: /boot/vmlinuz-4.9.0-17-686
Found initrd image: /boot/initrd.img-4.9.0-17-686
Found linux image: /boot/vmlinuz-4.9.0-16-686
Found initrd image: /boot/initrd.img-4.9.0-16-686
Found linux image: /boot/vmlinuz-4.9.0-7-686
Found initrd image: /boot/initrd.img-4.9.0-7-686
done
```

```console
root@debian:/home/yijinwang/Documents/OS_Lab5/kernel# dpkg -i linux-image-4.19.152-dbg_4.19.152-1_i386.deb
Selecting previously unselected package linux-image-4.19.152-dbg.
(Reading database ... 190735 files and directories currently installed.)
Preparing to unpack linux-image-4.19.152-dbg_4.19.152-1_i386.deb ...
Unpacking linux-image-4.19.152-dbg (4.19.152-1) ...
Setting up linux-image-4.19.152-dbg (4.19.152-1) ...
```

*(c) Reboot into the newly installed kernel. Show the output of **uname a.***

## Exercise 5

*Implement your own system call that (1) takes one argument (a nice value), (2) prints the current process’ PID and nice value, (3) changes the current process’ nice value to the nice value that is given as a parameter of the system call, and (4) finally returns the process’s PID to user space.* 

*(a) Implement your system call in sys.c using the appropriate macro,*

*(b) Add an entry for the new system call to the syscall_32.tbl for your appropriate architecture,* 

*(c) Compile, install, and boot the kernel containing your system call,* 

*(d) Implement a C application (in user space) that calls your newly implemented system call using the syscall function,*

*(e) Run your application and show that the system call is made by inspecting the strace.* 

*(f) Show that the nice value of the process has changed. TIPS: use the current macro, and set_user_nice function*

