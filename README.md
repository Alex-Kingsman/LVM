# LVM
Уменьшить том под / до 8G

Создаем pv

sudo pvcreate /dev/sdb

  Physical volume "/dev/sdb" successfully created.
  

alex@ubuntuserver:~$ sudo vgcreate vg_root /dev/sdb

  Volume group "vg_root" successfully created


sudo lvcreate -n lv_root -l +100%FREE /dev/vg_root

  Logical volume "lv_root" created.


sudo mkfs.ext4 /dev/vg_root/lv_root

mke2fs 1.47.0 (5-Feb-2023)
Creating filesystem with 2620416 4k blocks and 655360 inodes
Filesystem UUID: 12871863-9ccd-481b-ab9b-723a950d3a25
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done


sudo mount /dev/vg_root/lv_root  /mnt


sudo rsync -avxHAX --progress / /mnt/
sent 5,877,433,642 bytes  received 2,929,262 bytes  62,226,062.48 bytes/sec
total size is 5,869,652,804  speedup is 1.00

ls /mnt/

bin                cdrom  home   lib.usr-is-merged  mnt   root  sbin.usr-is-merged  swap.img  usr
bin.usr-is-merged  dev    lib    lost+found         opt   run   snap                sys       var
boot               etc    lib64  media              proc  sbin  srv                 tmp

grub-mkconfig -o /boot/grub/grub.cfg

Sourcing file `/etc/default/grub'
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-6.18.20-061820-generic
Found initrd image: /boot/initrd.img-6.18.20-061820-generic
Found linux image: /boot/vmlinuz-6.8.0-110-generic
Found initrd image: /boot/initrd.img-6.8.0-110-generic
Found linux image: /boot/vmlinuz-6.8.0-107-generic
Found initrd image: /boot/initrd.img-6.8.0-107-generic
Warning: os-prober will not be executed to detect other bootable partitions.
Systems on them will not be added to the GRUB boot configuration.
Check GRUB_DISABLE_OS_PROBER documentation entry.
Adding boot menu entry for UEFI Firmware Settings ...
done

update-initramfs -u

update-initramfs: Generating /boot/initrd.img-6.18.20-061820-generic


 lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda                         8:0    0   27G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    2G  0 part /boot
└─sda3                      8:3    0   23G  0 part
  └─ubuntu--vg-ubuntu--lv 252:1    0   23G  0 lvm
sdb                         8:16   0   10G  0 disk
└─vg_root-lv_root         252:0    0   10G  0 lvm  /
sdc                         8:32   0    2G  0 disk
sdd                         8:48   0    1G  0 disk
sde                         8:64   0    1G  0 disk
sr0                        11:0    1 1024M  0 rom

lvremove /dev/ubuntu-vg/ubuntu-lv

Do you really want to remove and DISCARD active logical volume ubuntu-vg/ubuntu-lv? [y/n]: y
  Logical volume "ubuntu-lv" successfully removed.


lvcreate -n ubuntu-vg/ubuntu-lv -L 8G /dev/ubuntu-vg

WARNING: ext4 signature detected on /dev/ubuntu-vg/ubuntu-lv at offset 1080. Wipe it? [y/n]: y
  Wiping ext4 signature on /dev/ubuntu-vg/ubuntu-lv.
  Logical volume "ubuntu-lv" created.

mkfs.ext4 /dev/ubuntu-vg/ubuntu-lv

mke2fs 1.47.0 (5-Feb-2023)
Creating filesystem with 2097152 4k blocks and 524288 inodes
Filesystem UUID: d13df8e3-c924-4cb6-9b02-808a1c70389a
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done

rsync -avxHAX --progress / /mnt/

sent 5,894,407,706 bytes  received 2,929,342 bytes  61,752,220.40 bytes/sec
total size is 5,886,619,252  speedup is 1.00

for i in /proc/ /sys/ /dev/ /run/ /boot/; do mount --bind $i /mnt/$i; done

# chroot /mnt/


grub-mkconfig -o /boot/grub/grub.cfg

Sourcing file `/etc/default/grub'
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-6.18.20-061820-generic
Found initrd image: /boot/initrd.img-6.18.20-061820-generic
Found linux image: /boot/vmlinuz-6.8.0-110-generic
Found initrd image: /boot/initrd.img-6.8.0-110-generic
Found linux image: /boot/vmlinuz-6.8.0-107-generic
Found initrd image: /boot/initrd.img-6.8.0-107-generic
Warning: os-prober will not be executed to detect other bootable partitions.
Systems on them will not be added to the GRUB boot configuration.
Check GRUB_DISABLE_OS_PROBER documentation entry.
Adding boot menu entry for UEFI Firmware Settings ...
done


update-initramfs -u

update-initramfs: Generating /boot/initrd.img-6.18.20-061820-generic
W: Couldn't identify type of root file system for fsck hook

root@ubuntuserver:/# pvcreate /dev/sdc /dev/sdd

  Physical volume "/dev/sdc" successfully created.
  Physical volume "/dev/sdd" successfully created.


 vgcreate vg_var /dev/sdc /dev/sdd
 
  Volume group "vg_var" successfully created

lvcreate -L 950M -m1 -n lv_var vg_var

  Rounding up size to full physical extent 952.00 MiB
  Logical volume "lv_var" created.

mkfs.ext4 /dev/vg_var/lv_var

mke2fs 1.47.0 (5-Feb-2023)
Creating filesystem with 243712 4k blocks and 60928 inodes
Filesystem UUID: 0d0ee013-faab-4596-b527-d06486040d99
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376

Allocating group tables: done
Writing inode tables: done
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done

mount /dev/vg_var/lv_var /mnt

cp -aR /var/* /mnt/


mkdir /tmp/oldvar && mv /var/* /tmp/oldvar

umount /mnt

mount /dev/vg_var/lv_var /var

echo "`blkid | grep var: | awk '{print $2}'` \
 /var ext4 defaults 0 0" >> /etc/fstab
 
 
 lvcreate -n LogVol_Home -L 2G /dev/ubuntu-vg
 
  Logical volume "LogVol_Home" created.

lvcreate -n LogVol_Home -L 2G /dev/ubuntu-vg

  Logical volume "LogVol_Home" created.

root@ubuntuserver:~# mkfs.ext4 /dev/ubuntu-vg/LogVol_Home

mke2fs 1.47.0 (5-Feb-2023)
Creating filesystem with 524288 4k blocks and 131072 inodes
Filesystem UUID: 77b74cb5-83b0-4f6c-9027-84e4043a17ff
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done

 mount /dev/ubuntu-vg/LogVol_Home /mnt/

cp -aR /home/* /mnt/

rm -rf /home/*

umount /mnt

mount /dev/ubuntu-vg/LogVol_Home /home/

echo "`blkid | grep Home | awk '{print $2}'` \
 /home xfs defaults 0 0" >> /etc/fstab

 touch /home/file{1..20}
 
lvcreate -L 100MB -s -n home_snap \
 /dev/ubuntu-vg/LogVol_Home
  Logical volume "home_snap" created.


rm -f /home/file{11..20}


lvconvert --merge /dev/ubuntu-vg/home_snap

  Merging of volume ubuntu-vg/home_snap started.
  ubuntu-vg/LogVol_Home: Merged: 100.00%
  
   systemctl daemon-reload
   
   ls -al /home
total 28
drwxr-xr-x  4 root root  4096 Apr 16 10:13 .
drwxr-xr-x 23 root root  4096 Apr 16 09:44 ..
drwxr-x---  5 alex alex  4096 Apr  6 13:29 alex
-rw-r--r--  1 root root     0 Apr 16 10:13 file1
-rw-r--r--  1 root root     0 Apr 16 10:13 file10
-rw-r--r--  1 root root     0 Apr 16 10:13 file11
-rw-r--r--  1 root root     0 Apr 16 10:13 file12
-rw-r--r--  1 root root     0 Apr 16 10:13 file13
-rw-r--r--  1 root root     0 Apr 16 10:13 file14
-rw-r--r--  1 root root     0 Apr 16 10:13 file15
-rw-r--r--  1 root root     0 Apr 16 10:13 file16
-rw-r--r--  1 root root     0 Apr 16 10:13 file17
-rw-r--r--  1 root root     0 Apr 16 10:13 file18
-rw-r--r--  1 root root     0 Apr 16 10:13 file19
-rw-r--r--  1 root root     0 Apr 16 10:13 file2
-rw-r--r--  1 root root     0 Apr 16 10:13 file20
-rw-r--r--  1 root root     0 Apr 16 10:13 file3
-rw-r--r--  1 root root     0 Apr 16 10:13 file4
-rw-r--r--  1 root root     0 Apr 16 10:13 file5
-rw-r--r--  1 root root     0 Apr 16 10:13 file6
-rw-r--r--  1 root root     0 Apr 16 10:13 file7
-rw-r--r--  1 root root     0 Apr 16 10:13 file8
-rw-r--r--  1 root root     0 Apr 16 10:13 file9
drwx------  2 root root 16384 Apr 16 10:12 lost+found
