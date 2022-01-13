# Lab 5

**Members: Fangwen Liao (3439869) | Yijin Wang (3476217)**

## Exercise 1
*(a)*
When a process hold a spin lock, it will loop until it gets the resource, it
should not wait for a long time. When holding a semaphore, a process can go to
sleep, so a process can not go to sleep while holding a spin lock, or when it
sleeps it can not release the spin lock.

*(b)*
The process holding a spin lock and a semaphore can go to sleep and never
release the spin lock, this may causing deadlock, or it should go to 
sleep, but spinning and waste cpu cycles.

## Exercise 2
*(a)*
When a process holding a spin lock is preempted, it doesn't release the lock,
if the preempting process need the resource, it will never get it, thus forming
a deadlock.

*(b)*
In a non-preemptible kernel, a process will release the resource only when it
finish using it and will not causing a dead lock for the next process.

## Exercise 3
*(a)*
when Thread0 aquire the semaphore, the count should be from 1 to zero, when
down() not automaticaly runned, Thread1 can also get the resource, thus causing
data inconsistancy.
When Thread0 finishes using resrouce and release the semaphore, when up() not
excuted automatically, Thread1 will never be able to get the resource.

## Exercise 4
*(a)*
The provided sh files have problems when running, to solve this problem, we use
dos2unix command to convert the file, or we
can also just copy them to a new file and run them inside the virtual machine.

```console
fangwenliao@debian:~/Downloads/OS_Lab5/kernel$ uname -a
Linux debian 4.9.0-16-686 #1 SMP Debian 4.9.272-2 (2021-07-19) i686 GNU/Linux
```
*(b)*
To run the compile.sh, we first need to apt install time or it will have errors
when running the script, or we can just delete the time, since only the make
command really matters. After the
compile.sh is done, change to root user and use the -i option of dkpg to install the
pakage.

*(c)*
```console
fangwenliao@debian:~$ uname -a
Linux debian 4.19.152 #1 SMP Sat Jan 8 08:30:56 CET 2022 i686 GNU/Linux
```

## Exercise 5
*(a)*
our code is as below:
```c
SYSCALL_DEFINE1(newcall, int, nice_value)
{
        int pid = task_tgid_vnr(current);
        struct task_struct *p;
        p = find_task_by_vpid(pid);
        int ni = task_nice(p);
        printk("PID: %d, Nice Value:%d.\n", pid, ni);
        set_user_nice(p, nice_value);
        return pid;
}
```
*(b)*
we enter the following code in syscall_32.tbl
```
387     i386    newcall                 sys_newcall                     __ia32_sys_newcall
```


