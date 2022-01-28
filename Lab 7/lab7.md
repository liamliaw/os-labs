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
```

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
According to man page of pvcreate, this command is used to initialize the
physical volume for later use by LVM.
```console
root@debian:/home/fangwenliao# pvcreate /dev/sdb 
  Physical volume "/dev/sdb" successfully created.
```

*(e)*
```console
root@debian:/home/fangwenliao# vgcreate newgroup /dev/sdb 
  Volume group "newgroup" successfully created
root@debian:/home/fangwenliao# 
```
*(f)*
```console
root@debian:/home/fangwenliao# lvcreate -l 50%VG newgroup -n newvol0
  Logical volume "newvol0" created.
root@debian:/home/fangwenliao# lvcreate -l 50%VG newgroup -n newvol1
  Logical volume "newvol1" created.
```
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
├─newgroup-newvol0    254:5    0 1020M  0 lvm
└─newgroup-newvol1    254:6    0 1020M  0 lvm
sr0                    11:0    1 1024M  0 rom
```
*(e)*
```console
root@debian:/home/fangwenliao# mkfs -t ext4 /dev/newgroup/newvol0
mke2fs 1.43.4 (31-Jan-2017)
Creating filesystem with 261120 4k blocks and 65280 inodes
Filesystem UUID: bb64650a-50fe-4eaf-902b-a948cf69ed55
Superblock backups stored on blocks:
	32768, 98304, 163840, 229376

Allocating group tables: done
Writing inode tables: done
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done

root@debian:/home/fangwenliao# mkfs -t ext4 /dev/newgroup/newvol1
mke2fs 1.43.4 (31-Jan-2017)
Creating filesystem with 261120 4k blocks and 65280 inodes
Filesystem UUID: 7ff98e7a-455d-4354-85b6-5b6fe4e45020
Superblock backups stored on blocks:
	32768, 98304, 163840, 229376

Allocating group tables: done
Writing inode tables: done
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done
```
*(h)*
```console
root@debian:/mnt# mkdir vol0 vol1 | ls
vol0  vol1
```
```console
root@debian:/mnt# mount /dev/newgroup/newvol0 vol0/
root@debian:/mnt# mount /dev/newgroup/newvol1 vol1/
```
*(i)*
```console
root@debian:/home/fangwenliao# pvs
  PV         VG        Fmt  Attr PSize  PFree
  /dev/sda5  debian-vg lvm2 a--  63.76g    0
  /dev/sdb   newgroup  lvm2 a--   2.00g 4.00m
root@debian:/home/fangwenliao# vgs
  VG        #PV #LV #SN Attr   VSize  VFree
  debian-vg   1   5   0 wz--n- 63.76g    0
  newgroup    1   2   0 wz--n-  2.00g 4.00m
root@debian:/home/fangwenliao# lvs
  LV      VG        Attr       LSize    Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  home    debian-vg -wi-ao----   44.71g
  root    debian-vg -wi-ao----   11.96g
  swap_1  debian-vg -wi-ao----    2.00g
  tmp     debian-vg -wi-ao----  816.00m
  var     debian-vg -wi-ao----    4.30g
  newvol0 newgroup  -wi-ao---- 1020.00m
  newvol1 newgroup  -wi-ao---- 1020.00m
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
├─newgroup-newvol0    254:5    0 1020M  0 lvm  /mnt/vol0
└─newgroup-newvol1    254:6    0 1020M  0 lvm  /mnt/vol1
sr0                    11:0    1 1024M  0 rom
```

## Exercise 2
*(a)*
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
├─newgroup-newvol0    254:0    0 1020M  0 lvm
└─newgroup-newvol1    254:1    0 1020M  0 lvm
sdc                     8:32   0    2G  0 disk
sdd                     8:48   0    2G  0 disk
sde                     8:64   0    2G  0 disk
sr0                    11:0    1 1024M  0 rom  
```
*(b)*
```console
root@debian:/home/fangwenliao# mdadm --create /dev/md/newraid --level=5 --raid-device=3 /dev/sdc /dev/sdd /dev/sde
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md/newraid started.
```
*(c)*
```console
root@debian:/home/fangwenliao# mdadm --create /dev/md/newraid --level=5 --raid-device=3 /dev/sdc /dev/sdd /dev/sde
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md/newraid started.
root@debian:/home/fangwenliao# mdadm --detail
mdadm: No devices given.
root@debian:/home/fangwenliao# mdadm --detail /dev/md/newraid
/dev/md/newraid:
        Version : 1.2
  Creation Time : Fri Jan 28 19:47:25 2022
     Raid Level : raid5
     Array Size : 4190208 (4.00 GiB 4.29 GB)
  Used Dev Size : 2095104 (2046.00 MiB 2145.39 MB)
   Raid Devices : 3
  Total Devices : 3
    Persistence : Superblock is persistent

    Update Time : Fri Jan 28 19:47:44 2022
          State : clean
 Active Devices : 3
Working Devices : 3
 Failed Devices : 0
  Spare Devices : 0

         Layout : left-symmetric
     Chunk Size : 512K

           Name : debian:newraid  (local to host debian)
           UUID : 09eb1e7c:f65c7df7:471d2c2d:1dfbd666
         Events : 19

    Number   Major   Minor   RaidDevice State
       0       8       32        0      active sync   /dev/sdc
       1       8       48        1      active sync   /dev/sdd
       3       8       64        2      active sync   /dev/sde
```
*(d)*
```consoel
root@debian:/home/fangwenliao# pvcreate /dev/md/newraid
  Physical volume "/dev/md/newraid" successfully created.
root@debian:/home/fangwenliao# vgcreate newgroup1 /dev/md/newraid
  Volume group "newgroup1" successfully created
root@debian:/home/fangwenliao# lvcreate -l 100%VG newgroup1 -n newvolume
  Logical volume "newvolume" created.
```
*(e)*
```console
root@debian:/home/fangwenliao# mkfs -t ext4 /dev/newgroup1/newvolume
mke2fs 1.43.4 (31-Jan-2017)
Creating filesystem with 1046528 4k blocks and 261632 inodes
Filesystem UUID: 7506c81d-a53e-4eea-a260-f47ae4feb4e7
Superblock backups stored on blocks:
	32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done
```
*(f)*
```console
root@debian:/home/fangwenliao# mkdir /mnt/vol2
root@debian:/home/fangwenliao# mount /dev/newgroup1/newvolume /mnt/vol2
root@debian:/home/fangwenliao# cd /mnt/vol2/
root@debian:/mnt/vol2# touch text.txt
root@debian:/mnt/vol2# vim text.txt
root@debian:/mnt/vol2# cat text.txt
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
some texts
```
*(h)*
```console
root@debian:/mnt/vol2# pvs
  PV         VG        Fmt  Attr PSize  PFree
  /dev/md127 newgroup1 lvm2 a--   3.99g    0
  /dev/sda5  debian-vg lvm2 a--  63.76g    0
  /dev/sdb   newgroup  lvm2 a--   2.00g 4.00m
root@debian:/mnt/vol2# vgs
  VG        #PV #LV #SN Attr   VSize  VFree
  debian-vg   1   5   0 wz--n- 63.76g    0
  newgroup    1   2   0 wz--n-  2.00g 4.00m
  newgroup1   1   1   0 wz--n-  3.99g    0
root@debian:/mnt/vol2# lvs
  LV        VG        Attr       LSize    Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  home      debian-vg -wi-ao----   44.71g
  root      debian-vg -wi-ao----   11.96g
  swap_1    debian-vg -wi-ao----    2.00g
  tmp       debian-vg -wi-ao----  816.00m
  var       debian-vg -wi-ao----    4.30g
  newvol0   newgroup  -wi-a----- 1020.00m
  newvol1   newgroup  -wi-a----- 1020.00m
  newvolume newgroup1 -wi-ao----    3.99g
  root@debian:/mnt/vol2# mdadm -detail
mdadm: -d does not set the mode, and so cannot be the first option.
root@debian:/mnt/vol2# mdadm --detail
mdadm: No devices given.
root@debian:/mnt/vol2# mdadm --detail /dev/md
md/    md127
root@debian:/mnt/vol2# mdadm --detail /dev/md
md/    md127
root@debian:/mnt/vol2# mdadm --detail /dev/md/newraid
/dev/md/newraid:
        Version : 1.2
  Creation Time : Fri Jan 28 19:47:25 2022
     Raid Level : raid5
     Array Size : 4190208 (4.00 GiB 4.29 GB)
  Used Dev Size : 2095104 (2046.00 MiB 2145.39 MB)
   Raid Devices : 3
  Total Devices : 3
    Persistence : Superblock is persistent

    Update Time : Fri Jan 28 20:08:58 2022
          State : clean
 Active Devices : 3
Working Devices : 3
 Failed Devices : 0
  Spare Devices : 0

         Layout : left-symmetric
     Chunk Size : 512K

           Name : debian:newraid  (local to host debian)
           UUID : 09eb1e7c:f65c7df7:471d2c2d:1dfbd666
         Events : 19

    Number   Major   Minor   RaidDevice State
       0       8       32        0      active sync   /dev/sdc
       1       8       48        1      active sync   /dev/sdd
       3       8       64        2      active sync   /dev/sde
root@debian:/mnt/vol2# lsblk
NAME                    MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
sda                       8:0    0   64G  0 disk
├─sda1                    8:1    0  243M  0 part  /boot
├─sda2                    8:2    0    1K  0 part
└─sda5                    8:5    0 63.8G  0 part
  ├─debian--vg-root     254:2    0   12G  0 lvm   /
  ├─debian--vg-var      254:3    0  4.3G  0 lvm   /var
  ├─debian--vg-swap_1   254:4    0    2G  0 lvm   [SWAP]
  ├─debian--vg-tmp      254:5    0  816M  0 lvm   /tmp
  └─debian--vg-home     254:6    0 44.7G  0 lvm   /home
sdb                       8:16   0    2G  0 disk
├─newgroup-newvol0      254:0    0 1020M  0 lvm
└─newgroup-newvol1      254:1    0 1020M  0 lvm
sdc                       8:32   0    2G  0 disk
└─md127                   9:127  0    4G  0 raid5
  └─newgroup1-newvolume 254:7    0    4G  0 lvm   /mnt/vol2
sdd                       8:48   0    2G  0 disk
└─md127                   9:127  0    4G  0 raid5
  └─newgroup1-newvolume 254:7    0    4G  0 lvm   /mnt/vol2
sde                       8:64   0    2G  0 disk
└─md127                   9:127  0    4G  0 raid5
  └─newgroup1-newvolume 254:7    0    4G  0 lvm   /mnt/vol2
sr0                      11:0    1 1024M  0 rom
```




