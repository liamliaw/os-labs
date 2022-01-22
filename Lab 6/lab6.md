# Lab 5

**Members: Fangwen Liao (3439869) | Yijin Wang (3476217)**

## Exercise 1
*(a)*
We use the -m option for free command to show the MiB format to make it
slightly more human readable.
```console
fangwenliao@debian:~$ free -m
              total        used        free      shared  buff/cache   available
Mem:           2019          38        1730           3         250        1767
Swap:          2043           0        2043
```
the total physical memory is 2019 MiB.
*(b)*
The total swap is 2043 MiB.

## Exercise 2
*(a)*
Our code is below, according to the manpage of malloc, it returns a pointer of
the allocated memory, and if the size is 0 it will return NULL:
```c
#include <stdio.h>
#include <stdlib.h>

#define BLOCK_SIZE 100 * 1048576

int main()
{
        int i;
        long *j;

        i = 0;
        j = NULL;
        while(1)
        {
                j = malloc(BLOCK_SIZE);
                if (j == NULL)
                {
                        break;
                }
                i++;
        }
        printf("Allocated %d MiB memory.\n", i * 100);
        return 0;
}
```
compile and run the program:
```console
fangwenliao@debian:~/Downloads$ gcc single_allocate.c -o single_allocate
fangwenliao@debian:~/Downloads$ ./single_allocate
Allocated 3000 MiB memory.
```
and the result is 3000 MiB.

*(b)*
We check the config file in /boot:
```console
fangwenliao@debian:~/Downloads$ cat /boot/config-4.9.0-16-686 | grep CONFIG_VMSPLIT
CONFIG_VMSPLIT_3G=y
# CONFIG_VMSPLIT_3G_OPT is not set
# CONFIG_VMSPLIT_2G is not set
# CONFIG_VMSPLIT_2G_OPT is not set
# CONFIG_VMSPLIT_1G is not set
```
As is shown the 3G barrier for user space memory is enabled, set a limit for a single
user space process, which prevent one
user space process consumes all the memory and save some memory for kernel space,
as the total memory space for 32 bit system is 2^32 bytes or 4 Gib.

## Exercise 3
*(a)*
Our code is below:
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define BLOCK_SIZE 512 * 1048576

int main()
{
        int  *i;
        long *j;
        int c;

        c = 1;
        i = malloc(BLOCK_SIZE);

        if(i == NULL)
        {
                printf("failed to allocate memory.\n");
        }

        j = memset(i, c, BLOCK_SIZE);

        while(1)
        {
        }

        return 0;
}
```
we first run free to see the status before the experiement, then start to open
multi processes.
```console
fangwenliao@debian:~/Downloads$ free -m
              total        used        free      shared  buff/cache   available
Mem:           2019          37        1735           3         246        1767
Swap:          2043           0        2043
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[1] 1051
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[2] 1052
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[3] 1053
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[4] 1054
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[5] 1055
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[6] 1056
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[7] 1057
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[8] 1058
fangwenliao@debian:~/Downloads$ failed to allocate memory.
^C
[8]+  Segmentation fault      ./multiple_allocate
fangwenliao@debian:~/Downloads$ jobs
[1]   Running                 ./multiple_allocate &
[2]   Running                 ./multiple_allocate &
[3]   Running                 ./multiple_allocate &
[4]   Running                 ./multiple_allocate &
[5]   Running                 ./multiple_allocate &
[6]-  Running                 ./multiple_allocate &
[7]+  Running                 ./multiple_allocate &
fangwenliao@debian:~/Downloads$ free -m
              total        used        free      shared  buff/cache   available
Mem:           2019        1869         126           0          23           1
Swap:          2043        1778         265
```
We use top command to inpect more details about the memory usage. We use the F
interactive command to customize its output format, the result is shown below,
we need the VIRT which is the total amount memomy wether used, swapped or
reserved, RES which is the acutally used in physical memory non swapped, and
SWAP which is the swapped amount:
```console
top - 19:42:31 up 5 min,  1 user,  load average: 6.92, 3.96, 1.65
Tasks: 130 total,   8 running, 122 sleeping,   0 stopped,   0 zombie
%Cpu(s): 99.6 us,  0.2 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem :  2068232 total,   124700 free,  1915456 used,    28076 buff/cache
KiB Swap:  2093052 total,   271768 free,  1821284 used.    84960 avail Mem 
  PID USER        VIRT    RES   SWAP COMMAND  
 skip some irrelevant lines...
 1056 fangwen+  526368 525136      0 multiple_alloca
 1051 fangwen+  526368     20 524316 multiple_alloca
 1057 fangwen+  526368 525124      0 multiple_alloca
 1054 fangwen+  526368 293820 230520 multiple_alloca
 1052 fangwen+  526368     20 524320 multiple_alloca
 1053 fangwen+  526368     12 524348 multiple_alloca
 1055 fangwen+  526368 525132      0 multiple_alloca
```
As is shown, there are 7 process sucessfully got the memory before the
segmentation error happens.
*(b)*
As is shown there are 7*526368KiB=3598MiB allocated in total, however only
3587Mib used in total. 
*(c)*
Thre rest free space from main memory and swap is 124700KiB+271768KiB=387MiB, which is not
enough for another process.

## Exercise 4
*(a)*
```console
fangwenliao@debian:~/Downloads$ fallocate -l 2048M new_swap.img
```
*(b)*
```console
fangwenliao@debian:~/Downloads$ su
Password:
root@debian:/home/fangwenliao/Downloads# mkswap new_swap.img
mkswap: new_swap.img: insecure permissions 0644, 0600 suggested.
mkswap: new_swap.img: insecure file owner 1000, 0 (root) suggested.
Setting up swapspace version 1, size = 2 GiB (2147479552 bytes)
no label, UUID=fd676918-cbd2-4ab1-8ad4-66443fe12da6
```
*(c)*
```console
root@debian:/home/fangwenliao/Downloads# swapon -p 0 new_swap.img
swapon: /home/fangwenliao/Downloads/new_swap.img: insecure permissions 0644, 0600 suggested.
swapon: /home/fangwenliao/Downloads/new_swap.img: insecure file owner 1000, 0 (root) suggested.
root@debian:/home/fangwenliao/Downloads# man swapon
```
*(d)*
```console
root@debian:/home/fangwenliao/Downloads# swapon --show
NAME                                     TYPE      SIZE USED PRIO
/dev/dm-2                                partition   2G   0B   -1
/home/fangwenliao/Downloads/new_swap.img file        2G   0B    0
```
*(e)*
```console
fangwenliao@debian:~/Downloads$ free -m
              total        used        free      shared  buff/cache   available
Mem:           2019          37        1733           3         248        1767
Swap:          4091           0        4091
```

## Exercise 5
```console
fangwenliao@debian:~/Downloads$ free -m
              total        used        free      shared  buff/cache   available
Mem:           2019        1856         120           0          43           6
Swap:          4091        3675         416
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[1] 1045
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[2] 1046
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[3] 1047
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[4] 1048
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[5] 1049
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[6] 1052
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[7] 1053
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[8] 1054
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[9] 1055
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[10] 1056
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[11] 1057
fangwenliao@debian:~/Downloads$ ./multiple_allocate &
[12] 1060
fangwenliao@debian:~/Downloads$ failed to allocate memory.
^C
[12]+  Segmentation fault      ./multiple_allocate
fangwenliao@debian:~/Downloads$ jobs
[1]   Running                 ./multiple_allocate &
[2]   Running                 ./multiple_allocate &
[3]   Running                 ./multiple_allocate &
[4]   Running                 ./multiple_allocate &
[5]   Running                 ./multiple_allocate &
[6]   Running                 ./multiple_allocate &
[7]   Running                 ./multiple_allocate &
[8]   Running                 ./multiple_allocate &
[9]   Running                 ./multiple_allocate &
[10]-  Running                 ./multiple_allocate &
[11]+  Running                 ./multiple_allocate &
```
We use the top again like in Ex3:
```console
top - 19:59:59 up 5 min,  1 user,  load average: 10.81, 6.16, 2.56
Tasks: 131 total,  12 running, 119 sleeping,   0 stopped,   0 zombie
%Cpu(s): 99.8 us,  0.1 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  0.1 si,  0.0 st
KiB Mem :  2068104 total,   125916 free,  1918896 used,    23292 buff/cache
KiB Swap:  4190200 total,   282124 free,  3908076 used.    83992 avail Mem
PID USER        VIRT    RES   SWAP COMMAND
 1054 fangwen+  526368 305492 219028 multiple_alloca
 1057 fangwen+  526368 525140      0 multiple_alloca
 1052 fangwen+  526368      8 524344 multiple_alloca
 1055 fangwen+  526368 525088      0 multiple_alloca
 1045 fangwen+  526368      8 524344 multiple_alloca
 1046 fangwen+  526368      8 524344 multiple_alloca
 1048 fangwen+  526368      8 524348 multiple_alloca
 1047 fangwen+  526368      8 524348 multiple_alloca
 1053 fangwen+  526368      8 524344 multiple_alloca
 1056 fangwen+  526368 525164      0 multiple_alloca
 1049 fangwen+  526368      8 524344 multiple_alloca
```
We have 11 process running this time. A total amount of 11*526368KiB=5654MiB
memory is allocated, which accually 5635MiB is used. The rest available memory
which is 125916KiB+282124KiB=389MiB is not enough for a new process.
From Ex3 and Ex5, we know each process using 514 MiB memory, compared to Ex3, 
the swap have increased for 2048MiB, which is a little bit smaller than the
space for 4 process, however since each process can partailly swapped, with the
help of the few rest main and swap memory, the
total memory plus new swap is enough to increase four new process. 
