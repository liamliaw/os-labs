# Lab 7

**Members: Fangwen Liao (3439869) | Yijin Wang (3476217)**

## Exercise 1
*(a)*
According to the man page of lsblk, it is used to show information of all block
devices. The result in our machine is below:
```console
fangwenliao@debian:~$ lsblk
NAME                  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda                     8:0    0   64G  0 disk
├─sda1                  8:1    0  243M  0 part /boot
├─sda2                  8:2    0    1K  0 part
└─sda5                  8:5    0 63.8G  0 part
  ├─debian--vg-root   254:0    0   12G  0 lvm  /
  ├─debian--vg-var    254:1    0  4.3G  0 lvm  /var
  ├─debian--vg-swap_1 254:2    0    2G  0 lvm  [SWAP]
  ├─debian--vg-tmp    254:3    0  816M  0 lvm  /tmp
  └─debian--vg-home   254:4    0 44.7G  0 lvm  /home
sr0                    11:0    1 1024M  0 rom
fangwenliao@debian:~$ ls -l /dev/sr0
brw-rw---- 1 root cdrom 11, 0 Jan 28 21:51 /dev/sr0
```
There are two hard drives, sda which is the first SATA device and sr0 which is
the cdrom. sda is has three partitions, in sda5 there are several logical
partitions.

*(b)*
We add a 2GB vdi disk in virtualbox.
*(C)*
After adding the new disk the result is below:
```console
fangwenliao@debian:~$ lsblk
NAME                  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda                     8:0    0   64G  0 disk
├─sda1                  8:1    0  243M  0 part /boot
├─sda2                  8:2    0    1K  0 part
└─sda5                  8:5    0 63.8G  0 part
  ├─debian--vg-root   254:0    0   12G  0 lvm  /
  ├─debian--vg-var    254:1    0  4.3G  0 lvm  /var
  ├─debian--vg-swap_1 254:2    0    2G  0 lvm  [SWAP]
  ├─debian--vg-tmp    254:3    0  816M  0 lvm  /tmp
  └─debian--vg-home   254:4    0 44.7G  0 lvm  /home
sdb                     8:16   0    2G  0 disk
sr0                    11:0    1 1024M  0 rom
```
As we can see there is a new sdb device.

*(d)*
According to man page of lvm(8), the pvcreate command is used to initilize the
physical volume for later use by LVM.
```console
root@debian:/home/fangwenliao# pvcreate /dev/sdb 
  Physical volume "/dev/sdb" successfully created.
```

*(e)*
From the manpage of lvm, the vgcreate command is used to create a new volume
group:
```console
root@debian:/home/fangwenliao# vgcreate newgroup0 /dev/sdb
  Volume group "newgroup0" successfully created
```
*(f)*
According to man page of lvm, the lvcreate command is to create a logical
volume in a exisiting group, according to the man page of lvcreate, we use -l
to set the 50% size and -n to name the new volume:
```console
root@debian:/home/fangwenliao# lvcreate -l 50%VG newgroup0 -n newvol0
  Logical volume "newvol0" created.
root@debian:/home/fangwenliao# lvcreate -l 50%VG newgroup0 -n newvol1
  Logical volume "newvol1" created.
```
*(g)*
We use the mkfs command to create the file system, use -t with ext4 to set the
type of file system to be ext4:
```console
root@debian:/home/fangwenliao# mkfs -t ext4 /dev/newgroup0/newvol0
mke2fs 1.43.4 (31-Jan-2017)
Creating filesystem with 261120 4k blocks and 65280 inodes
Filesystem UUID: 73dbadc5-449e-487f-92d3-2bca05fb80a9
Superblock backups stored on blocks:
	32768, 98304, 163840, 229376

Allocating group tables: done
Writing inode tables: done
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done

root@debian:/home/fangwenliao# mkfs -t ext4 /dev/newgroup0/newvol1
mke2fs 1.43.4 (31-Jan-2017)
Creating filesystem with 261120 4k blocks and 65280 inodes
Filesystem UUID: c6926ded-108e-4e48-91e5-91af8d999b3c
Superblock backups stored on blocks:
	32768, 98304, 163840, 229376

Allocating group tables: done
Writing inode tables: done
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done
```
*(h)*
The mount command is used to mount file systems. We mount them under the /mnt
directory, which can be used for mounting devices:
```console
root@debian:/home/fangwenliao# mkdir /mnt/vol0 /mnt/vol1
root@debian:/home/fangwenliao# mount /dev/newgroup0/newvol0 /mnt/vol0/
root@debian:/home/fangwenliao# mount /dev/newgroup0/newvol1 /mnt/vol1/
```
*(i)*
According to man page of lvm, psv, vgs and lvs are used to report information about physical
volumes, volumes groups and logical volumes:
```console
root@debian:/home/fangwenliao# pvs
  PV         VG        Fmt  Attr PSize  PFree
  /dev/sda5  debian-vg lvm2 a--  63.76g    0 
  /dev/sdb   newgroup0 lvm2 a--   2.00g 4.00m
root@debian:/home/fangwenliao# vgs
  VG        #PV #LV #SN Attr   VSize  VFree
  debian-vg   1   5   0 wz--n- 63.76g    0 
  newgroup0   1   2   0 wz--n-  2.00g 4.00m
root@debian:/home/fangwenliao# lvs
  LV      VG        Attr       LSize    Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  home    debian-vg -wi-ao----   44.71g                                                    
  root    debian-vg -wi-ao----   11.96g                                                    
  swap_1  debian-vg -wi-ao----    2.00g                                                    
  tmp     debian-vg -wi-ao----  816.00m                                                    
  var     debian-vg -wi-ao----    4.30g                                                    
  newvol0 newgroup0 -wi-ao---- 1020.00m                                                    
  newvol1 newgroup0 -wi-ao---- 1020.00m 
root@debian:/home/fangwenliao# lsblk
NAME                  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda                     8:0    0   64G  0 disk 
├─sda1                  8:1    0  243M  0 part /boot
├─sda2                  8:2    0    1K  0 part 
└─sda5                  8:5    0 63.8G  0 part 
  ├─debian--vg-root   254:0    0   12G  0 lvm  /
  ├─debian--vg-var    254:1    0  4.3G  0 lvm  /var
  ├─debian--vg-swap_1 254:2    0    2G  0 lvm  [SWAP]
  ├─debian--vg-tmp    254:3    0  816M  0 lvm  /tmp
  └─debian--vg-home   254:4    0 44.7G  0 lvm  /home
sdb                     8:16   0    2G  0 disk 
├─newgroup0-newvol0   254:5    0 1020M  0 lvm  /mnt/vol0
└─newgroup0-newvol1   254:6    0 1020M  0 lvm  /mnt/vol1
sr0                    11:0    1 1024M  0 rom  
```

## Exercise 2
*(a)*
Three new devices sdc, sdd and sde are added:
```console
fangwenliao@debian:~$ lsblk
NAME                  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda                     8:0    0   64G  0 disk 
├─sda1                  8:1    0  243M  0 part /boot
├─sda2                  8:2    0    1K  0 part 
└─sda5                  8:5    0 63.8G  0 part 
  ├─debian--vg-root   254:2    0   12G  0 lvm  /
  ├─debian--vg-var    254:3    0  4.3G  0 lvm  /var
  ├─debian--vg-swap_1 254:4    0    2G  0 lvm  [SWAP]
  ├─debian--vg-tmp    254:5    0  816M  0 lvm  /tmp
  └─debian--vg-home   254:6    0 44.7G  0 lvm  /home
sdb                     8:16   0    2G  0 disk 
├─newgroup0-newvol0   254:0    0 1020M  0 lvm  
└─newgroup0-newvol1   254:1    0 1020M  0 lvm  
sdc                     8:32   0    2G  0 disk 
sdd                     8:48   0    2G  0 disk 
sde                     8:64   0    2G  0 disk 
sr0                    11:0    1 1024M  0 rom  
```
*(b)*
According to man page of mdadm, we  should use the create mode, and the level
should be 5 , should include sdc sdd and sde tree devices.
```console
root@debian:/home/fangwenliao# mdadm --create /dev/md127 --level=5 --raid-device=3 /dev/sdc /dev/sdd /dev/sde
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md127 started.
```
*(c)*
```console
root@debian:/home/fangwenliao# mdadm --detail /dev/md127 
/dev/md127:
        Version : 1.2
  Creation Time : Fri Jan 28 22:48:08 2022
     Raid Level : raid5
     Array Size : 4190208 (4.00 GiB 4.29 GB)
  Used Dev Size : 2095104 (2046.00 MiB 2145.39 MB)
   Raid Devices : 3
  Total Devices : 3
    Persistence : Superblock is persistent

    Update Time : Fri Jan 28 22:48:22 2022
          State : clean 
 Active Devices : 3
Working Devices : 3
 Failed Devices : 0
  Spare Devices : 0

         Layout : left-symmetric
     Chunk Size : 512K

           Name : debian:127  (local to host debian)
           UUID : d4a254f0:847b4926:8a8916b6:75d03211
         Events : 18

    Number   Major   Minor   RaidDevice State
       0       8       32        0      active sync   /dev/sdc
       1       8       48        1      active sync   /dev/sdd
       3       8       64        2      active sync   /dev/sde
```
*(d)*
```consoel
root@debian:/home/fangwenliao# pvcreate /dev/md127 
  Physical volume "/dev/md127" successfully created.
root@debian:/home/fangwenliao# vgcreate newgroup1 /dev/md127 
  Volume group "newgroup1" successfully created
root@debian:/home/fangwenliao# lvcreate -l 100%FREE newgroup1 -n newvol2
  Logical volume "newvol2" created.
```
*(e)*
```console
root@debian:/home/fangwenliao# mkfs.ext4 /dev/newgroup1/newvol2 
mke2fs 1.43.4 (31-Jan-2017)
Creating filesystem with 1046528 4k blocks and 261632 inodes
Filesystem UUID: d1cc5a04-2452-4019-b39c-88e000b9a8ea
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done 
```
*(f)*
```console
root@debian:/home/fangwenliao# mount /dev/newgroup1/newvol2 /mnt/vol2
root@debian:/home/fangwenliao# cd /mnt/vol2/
root@debian:/mnt/vol2# vim text
root@debian:/mnt/vol2# cat text 
some texts
some texts
some texts
some texts
```
*(g)*
*(h)*
```console
root@debian:/home/fangwenliao# pvs
  PV         VG        Fmt  Attr PSize  PFree
  /dev/md127 newgroup1 lvm2 a--   3.99g    0
  /dev/sda5  debian-vg lvm2 a--  63.76g    0
  /dev/sdb   newgroup0 lvm2 a--   2.00g 4.00m
root@debian:/home/fangwenliao# vgs
  VG        #PV #LV #SN Attr   VSize  VFree
  debian-vg   1   5   0 wz--n- 63.76g    0
  newgroup0   1   2   0 wz--n-  2.00g 4.00m
  newgroup1   1   1   0 wz--n-  3.99g    0
root@debian:/home/fangwenliao# lvs
  LV      VG        Attr       LSize    Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  home    debian-vg -wi-ao----   44.71g
  root    debian-vg -wi-ao----   11.96g
  swap_1  debian-vg -wi-ao----    2.00g
  tmp     debian-vg -wi-ao----  816.00m
  var     debian-vg -wi-ao----    4.30g
  newvol0 newgroup0 -wi-a----- 1020.00m
  newvol1 newgroup0 -wi-a----- 1020.00m
  newvol2 newgroup1 -wi-ao----    3.99g
root@debian:/home/fangwenliao# mdadm --detail /dev/newgroup1/newvol2
/dev/newgroup1/newvol2:
        Version : 1.2
  Creation Time : Fri Jan 28 22:48:08 2022
     Raid Level : raid5
     Array Size : 4186112 (3.99 GiB 4.29 GB)
  Used Dev Size : 2095104 (2046.00 MiB 2145.39 MB)
   Raid Devices : 3
  Total Devices : 3
    Persistence : Superblock is persistent

    Update Time : Fri Jan 28 23:00:41 2022
          State : clean
 Active Devices : 3
Working Devices : 3
 Failed Devices : 0
  Spare Devices : 0

         Layout : left-symmetric
     Chunk Size : 512K

           Name : debian:127  (local to host debian)
           UUID : d4a254f0:847b4926:8a8916b6:75d03211
         Events : 18

    Number   Major   Minor   RaidDevice State
       0       8       32        0      active sync   /dev/sdc
       1       8       48        1      active sync   /dev/sdd
       3       8       64        2      active sync   /dev/sde
root@debian:/home/fangwenliao# lsblk
NAME                  MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
sda                     8:0    0   64G  0 disk  
├─sda1                  8:1    0  243M  0 part  /boot
├─sda2                  8:2    0    1K  0 part  
└─sda5                  8:5    0 63.8G  0 part  
  ├─debian--vg-root   254:2    0   12G  0 lvm   /
  ├─debian--vg-var    254:3    0  4.3G  0 lvm   /var
  ├─debian--vg-swap_1 254:4    0    2G  0 lvm   [SWAP]
  ├─debian--vg-tmp    254:5    0  816M  0 lvm   /tmp
  └─debian--vg-home   254:6    0 44.7G  0 lvm   /home
sdb                     8:16   0    2G  0 disk  
├─newgroup0-newvol0   254:0    0 1020M  0 lvm   
└─newgroup0-newvol1   254:1    0 1020M  0 lvm   
sdc                     8:32   0    2G  0 disk  
└─md127                 9:127  0    4G  0 raid5 
  └─newgroup1-newvol2 254:7    0    4G  0 lvm   /mnt/vol2
sdd                     8:48   0    2G  0 disk  
└─md127                 9:127  0    4G  0 raid5 
  └─newgroup1-newvol2 254:7    0    4G  0 lvm   /mnt/vol2
sde                     8:64   0    2G  0 disk  
└─md127                 9:127  0    4G  0 raid5 
  └─newgroup1-newvol2 254:7    0    4G  0 lvm   /mnt/vol2
sr0                    11:0    1 1024M  0 rom   
```
## Exercise 3
*(a)*
```console
root@debian:/home/fangwenliao# mdadm --run /dev/md127 
mdadm: started array /dev/md/debian:127
root@debian:/home/fangwenliao# mdadm --detail /dev/md127 
/dev/md127:
        Version : 1.2
  Creation Time : Fri Jan 28 22:48:08 2022
     Raid Level : raid5
     Array Size : 4190208 (4.00 GiB 4.29 GB)
  Used Dev Size : 2095104 (2046.00 MiB 2145.39 MB)
   Raid Devices : 3
  Total Devices : 2
    Persistence : Superblock is persistent

    Update Time : Fri Jan 28 23:07:54 2022
          State : clean, degraded 
 Active Devices : 2
Working Devices : 2
 Failed Devices : 0
  Spare Devices : 0

         Layout : left-symmetric
     Chunk Size : 512K

           Name : debian:127  (local to host debian)
           UUID : d4a254f0:847b4926:8a8916b6:75d03211
         Events : 18

    Number   Major   Minor   RaidDevice State
       0       8       32        0      active sync   /dev/sdc
       1       8       48        1      active sync   /dev/sdd
       -       0        0        2      removed
```
*(b)*
```console
root@debian:/home/fangwenliao# mount /dev/newgroup1/newvol2 /mnt/vol2/
root@debian:/home/fangwenliao# cd /mnt/vol2/
root@debian:/mnt/vol2# ls
lost+found  text
root@debian:/mnt/vol2# cat text
some texts
some texts
some texts
some texts
```
*(c)*
[A RAID5 can only have 1 disk failure, if another disk fails, the whole array
will fail.](https://en.wikipedia.org/wiki/RAID#Correlated_failures)
*(d)*
Use RAID level 6, which can deal with multiple disk failures.

## Exercise 4
*(a)*
A new device is added
```console
angwenliao@debian:~$ lsblk
NAME                  MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
sda                     8:0    0   64G  0 disk
├─sda1                  8:1    0  243M  0 part  /boot
├─sda2                  8:2    0    1K  0 part
└─sda5                  8:5    0 63.8G  0 part
  ├─debian--vg-root   253:2    0   12G  0 lvm   /
  ├─debian--vg-var    253:3    0  4.3G  0 lvm   /var
  ├─debian--vg-swap_1 253:4    0    2G  0 lvm   [SWAP]
  ├─debian--vg-tmp    253:5    0  816M  0 lvm   /tmp
  └─debian--vg-home   253:6    0 44.7G  0 lvm   /home
sdb                     8:16   0    2G  0 disk
├─newgroup0-newvol0   253:0    0 1020M  0 lvm
└─newgroup0-newvol1   253:1    0 1020M  0 lvm
sdc                     8:32   0    2G  0 disk
└─md127                 9:127  0    4G  0 raid5
  └─newgroup1-newvol2 253:7    0    4G  0 lvm
sdd                     8:48   0    2G  0 disk
└─md127                 9:127  0    4G  0 raid5
  └─newgroup1-newvol2 253:7    0    4G  0 lvm
sde                     8:64   0    2G  0 disk
sr0                    11:0    1 1024M  0 rom
```
*(b)*
According to the man page of mdadm, the manage mode can be used to add or
remove device:
```console
root@debian:/home/fangwenliao# mdadm --manage /dev/md127 --add /dev/sde
mdadm: added /dev/sde
```
*(d)*
```console
root@debian:/home/fangwenliao# mdadm --detail /dev/md127
/dev/md127:
        Version : 1.2
  Creation Time : Fri Jan 28 22:48:08 2022
     Raid Level : raid5
     Array Size : 4190208 (4.00 GiB 4.29 GB)
  Used Dev Size : 2095104 (2046.00 MiB 2145.39 MB)
   Raid Devices : 3
  Total Devices : 3
    Persistence : Superblock is persistent

    Update Time : Fri Jan 28 23:31:28 2022
          State : clean
 Active Devices : 3
Working Devices : 3
 Failed Devices : 0
  Spare Devices : 0

         Layout : left-symmetric
     Chunk Size : 512K

           Name : debian:127  (local to host debian)
           UUID : d4a254f0:847b4926:8a8916b6:75d03211
         Events : 46

    Number   Major   Minor   RaidDevice State
       0       8       32        0      active sync   /dev/sdc
       1       8       48        1      active sync   /dev/sdd
       3       8       64        2      active sync   /dev/sde
```
