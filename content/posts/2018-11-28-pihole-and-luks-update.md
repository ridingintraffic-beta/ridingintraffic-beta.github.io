---
layout: single
title: 2018-11-28 pihole-and-luks-update
modified:
categories: blog
date: 2018-11-28T08:08:50-04:00
---

# pi-hole
Well i had tried to use pi-hole before and it failed horribly.  Tonight on the other hand, I just wiped a pi and installed an sdcard, ran a simple script and all the magic was done for me...   So I guess I might have to use this after all....  I am also going to do some research on automating the maintaince of it to make sure the block lists are also up to date and whatnot. I think i might have to write a blog post about this one next, its pretty easy but it needs a little bit more meat than what I just did.
But really what it comes down to is 
1. install raspbian lite
2. setup ssh
3. change hostname
4. set static ip on router
5. run `curl -sSL https://install.pi-hole.net | bash`
6. follow the prompts and get everything turned on.
7. pay attention to the last screen if you enabled the admin web page, it will give oyu the password
8. when you miss that page, ssh into the pihole and reset the password for the admin page with 
```
$ pihole -a -p
Enter New Password (Blank for no password): 
```
9. setup your laptop to use the dns server ip of the pi hole
OR
9. setup your router to use the dns server ip of the pi hole
done
[install-pi-hole](https://learn.adafruit.com/pi-hole-ad-blocker-with-pi-zero-w/install-pi-hole "install-pi-hole")


# luks tweak.
I recieved some feedback on my luks cracking article about the header size.
there was an extra command added to my gist that will identify exactly how big the header should be and therefore how much data is going to be needed to be copied.
`$ cryptsetup luksDump test | grep "Payload offset" # add 1 to the offset value that comes back`

now all the commands are
```
$ dd if=/dev/urandom of=test bs=1M count=100
$ cryptsetup luksFormat test  #use password password
$ cryptsetup luksOpen test tmp
$ xxd -l 512 /dev/mapper/tmp # is random data at this point
$ mkfs.ext4 /dev/mapper/tmp  # use the same file system that is used by your system/device
$ xxd -l 512 /dev/mapper/tmp # should no longer be random data
$ cryptsetup luksClose tmp
$ cryptsetup luksDump test | grep "Payload offset" # add 1 to the offset value that comes back
$ dd if=test of=luks-header bs=512 count=4097 # grabs header
$ echo "password" >>list
$ hashcat -m 14600 -a 0 -w 3 luks-header list -o found
```
or the link to the gist is : [luks-gist](https://gist.github.com/ridingintraffic/f463838d21dc9bab3f4e6674ab58d4f9 "luks-gist")


