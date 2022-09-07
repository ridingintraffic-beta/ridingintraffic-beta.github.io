---
layout: single
title: 2018-11-14 hashcat-luks
modified:
categories: blog
date: 2018-11-14T08:08:50-04:00
---

# luks encryption
lets create an encrypted container and toss some data in it...
```
$ dd if=/dev/urandom of=test bs=1M count=100
$ cryptsetup luksFormat test  #use password password
$ cryptsetup luksOpen test tmp
$ xxd -l 512 /dev/mapper/tmp # is random data at this point
$ mkfs.ext4 /dev/mapper/tmp  # use the same file system that is used by your system/device
$ xxd -l 512 /dev/mapper/tmp # should no longer be random data
$ cryptsetup luksClose tmp

```
After it is created if we wanted to crack it with hashcat then we would just need to grab some header data and run it though

```
$ dd if=test of=luks-header bs=512 count=4097 # grabs header
$ echo "password" >>list
$ hashcat -m 14600 -a 0 -w 3 luks-header list -o found
```
We also created a cheap password list in which we knew the password anyway but this is not an exercise with hashcat lists so much as it is breaking luks.


Great we broke it now lets open in
```
$ cryptsetup luksOpen test tmp # asks for password
$ # time to mount
$ mount /dev/mapper/tmp /mnt/files
$ # when done unmount and close
$ umount /mnt/files
$ cryptsetup luksClose tmp

```