---
layout: single
title: 2018-12-05 sudo-make-me-a-sandwich
modified:
categories: blog
date: 2018-12-05-T08:08:50-04:00
---
# sudo make me a sandwich, then I'll pwn your fridge

Sudo is a program for Unix-like computer operating systems that allows users to run programs with the security privileges of another user. 
[wikipedia-sudo](https://en.wikipedia.org/wiki/Sudo "wikipedia-sudo")

Let’s say that we have a folder named `/luggage/`. The luggage is carrying some incredibly valuable things. Rincewind and twoflower are two users who have been traveling with this luggage for sometime. Because, rincewind doesn't want twoflower to read the octavo, but is fine if he looks at the camera, both which are located in the in the luggage. `/luggage/camera/` `/luggage/octavo/` We have allowed twoflower to run only the cat command as rincewind, and only in the location `/luggage/camera/` folder. Because, we don’t know what the photos will be named we will wildcard all the filenames in `/luggage/camera/*`

Rincewind feels pretty good that twoflower will never be able to read anything in the octavo and certainly never be able to read the spell located in the folder and file permissions are both set with 0700. 
Below is the output of `/etc/sudoers.d/012-twoflower` file and a command output.
```
#/etc/sudoers.d/012-twoflower
twoflower discworld=(rincewind) /bin/cat /luggage/camera/*

root@discworld:/ $ ls -lA /luggage
total 8
drwxr-xr-x 2 twoflower twoflower 4096 Dec  5 02:37 camera
drwx------ 2 rincewind rincewind 4096 Dec  5 02:40 octavo

root@discworld:/luggage $ ls -la /luggage/octavo/
total 12
drwx------ 2 rincewind rincewind 4096 Dec  5 02:40 .
drwxrwxrwx 4 root      root      4096 Dec  5 02:37 ..
-rwx------ 1 rincewind rincewind   67 Dec  5 02:40 spell
```

We feel pretty good about this setup, it has only allowed twoflower a single command in a single path. Next we let twoflower have at it. And then something terrible happens.

```

$ twoflower@discworld:/luggage$ sudo -l
Matching Defaults entries for twoflower on discworld:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User twoflower may run the following commands on discworld:
    (rincewind) /bin/cat /luggage/camera/*
$ twoflower@discworld:/luggage$ sudo -u rincewind cat /luggage/octavo/spell
Sorry, user twoflower is not allowed to execute '/bin/cat /luggage/octavo/spell' as rincewind on discworld.

$ twoflower@discworld:/luggage$ sudo -u rincewind cat /luggage/camera/../octavo/spell
Ashonai. Ebiris. Urshoring. Kvanti. Pythan. N'gurad. Feringomalee.
twoflower@discworld:/luggage$
```
Twoflower has read the spell inside of the octavo and everything has ended. How? what? This is simply the way that the wildcard works with sudo. It’s just turtles, all the way down. We should never use wildcards like this in sudo, ever.

Next, lock it down, we will take away the cat command and replace it with just VI and then no arguments can be passed to VI, because if a wildcarded path is included then VI will fall to the same directory traversal issue.
 
```
root@discworld:~# cat /etc/sudoers.d/012_twoflower-nopasswd
twoflower discworld=(rincewind) /usr/bin/vi ""

twoflower@discworld:/luggage/camera$ sudo -u rincewind vi /luggage/camera/../octavo/spell
Sorry, user twoflower is not allowed to execute '/usr/bin/vi /luggage/camera/../octavo/spell' as rincewind on discworld.
## you shall not pass(arguments)!

twoflower@discworld:/luggage/camera$ sudo -u rincewind vi
<vim session opens>
~
:e ../octavo/spell<enter>
...
Ashonai. Ebiris. Urshoring. Kvanti. Pythan. N'gurad. Feringomalee.
## awe nuts you can open the file if you traverse directories after VI is open

## ...it gets worse
~<<still in vim>>
:!/bin/bash<enter>
<new shell session opens from vim>

rincewind@discworld:/luggage/camera$
rincewind@discworld:/luggage/camera$ cd ../octavo/
rincewind@discworld:/luggage/octavo$ cat spell
Ashonai. Ebiris. Urshoring. Kvanti. Pythan. N'gurad. Feringomalee.
rincewind@discworld:/luggage/octavo$
## world sets on fire :(
```

This is even worse because now, not only can twoflower still open read and edit the spell in the octavo, VIM has enabled a shell escape as the sudoer. This is because VIM is invoking the shell after the session is launched as the sudoer. Honey badger (VIM) don’t care what the sudoers.d/file said. 
Alright next step, take away VIM, give twoflower less and ONLY a single file in a single folder, because less has to be better.

```
root@discworld:~# cat /etc/sudoers.d/012_twoflower-nopasswd
twoflower discworld=(rincewind) /usr/bin/less /luggage/camera/picture


twoflower@discworld:/luggage/camera$ sudo -u rincewind less picture
Sorry, user twoflower is not allowed to execute '/usr/bin/less picture' as rincewind on discworld.
## got it i need the whole path

twoflower@discworld:/luggage/camera$ sudo -u rincewind less /luggage/camera/picture

<less session begins>
i see things
/luggage/camera/picture (END)
<type>:q<enter>
<quit less session>
## start the sudo less command and pass the picture file


## can i traverse?
twoflower@discworld:/luggage/camera$ sudo -u rincewind less /luggage/camera/../octavo/spell
Sorry, user twoflower is not allowed to execute '/usr/bin/less /luggage/camera/../octavo/spell' as rincewind on discworld.
## traverse failed
## but wait...
twoflower@discworld:/luggage/camera$ sudo -u rincewind less /luggage/camera/picture

<less session begins>
i see things
/luggage/camera/picture (END)
<type>!bash<enter>

<new bash session starts>
rincewind@discworld:/luggage/camera$ cat ../octavo/spell
Ashonai. Ebiris. Urshoring. Kvanti. Pythan. N'gurad. Feringomalee.
## gdi
## less has launched an escaped shell as rincewind and now we can read the spell (facepalm)
```

Sudo is a super powerful and incredibly dangerous tool. The commands that are allowed sudo access need to be thought out and the functionality needs to be understood. None of these examples are exploits, or overflows. They are all working exactly as designed. Understand the privileges that you are granting with sudo, otherwise someone will get into your fridge, eat all your food and then burn down your house.

Great source for deeper explanation of restricted shells and commands.
[escaping-restricted-linux-shells](https://pen-testing.sans.org/blog/2012/06/06/escaping-restricted-linux-shells "escaping-restricted-linux-shells")


