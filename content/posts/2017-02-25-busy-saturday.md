---
layout: single
title: 2017-02-25 ithub, git, jekyll, ruby, mac, docker, raspberrypi
tags : [github, git, jekyll, ruby, mac, docker, raspberrypi]
modified:
categories: blog
excerpt:
tags : [github, git, jekyll, ruby, mac, docker, raspberrypi]
image:
  feature:
date: 2017-02-25T08:08:50-04:00


---
# Productive Saturday

## rant
Been cranking away at my laptop from 630-830 this morning, and realizing that my ancient mac book air battery which was already replace once, is just a poor shadow of what it should be. I'm only going to get about 3 hours out of this battery before it dies.  Which is sad because i really wanted to get a new laptop this year but mac did not really give us any options. I don't have anything that runs usb-c so that would be dongle hell to switch to any of their new models.

## github
Finally decided that i am going to use github fro my private repos - which means I needed to clean up and migrate from bitbucket  kind of sad  but at the same time I am pruning out my old repos and getting rid of many years of ugly code history as well.  Especially important because when I ran a push from my base repo github would throw a fit with the pre receive commit, which I think narrowed down to having some old commit history in there where I thought it was a good idea to store some massive binary file in it.  Hey we can't all be perfect right?

## jekyll and ruby
Turns out i don't know ruby.   Which would make sense because I have never really touched anything ruby in my life but jekyll is kind of built on top of ruby and because I was trying to modify a theme which I failed at I had to go and update my brew and then try to reinstall the bundle and in the end managed to muck but my local jekyll preview.  Backed it all out and I will give it another go later. 

## docker pi
I thought it might be fun to try and stand up a docker host on one of the many unused raspberry pis that i have laying around.   I already have a docker host running on ubuntu in my basement but hey why not try a pi. 
A quick Internet search told me that it is supported with raspbian jessie?  [docker-pi](https://www.raspberrypi.org/blog/docker-comes-to-raspberry-pi/ "docker-pi") Commence the download and some command line kung-fu to get it written to the sd card: 
```
diskutil list
diskutil unmountDisk /dev/disk2
sudo dd bs=1m if=2017-01-11-raspbian-jessie-lite.img of=/dev/rdisk
```
Few minutes later I get pi, Then do a uick hdmi monitor and keyboard plugin 
```
sudo rasp-config
enable ssh
grow filesystem
reboot
```
Next did some ssh into the host and fire off
```
curl -sSL https://get.docker.com | sh
``` 
Magically docker is installed on the pi  WOO!! Well kind of, I was quickly reminded why 
I had not gotten around to doing this because! 

```
Know your Architecture
The Raspberry Pi hardware architecture is called ARM and differs from the architecture behind your regular PC, laptop or cloud instance.
```
[pi is an ARM](http://blog.alexellis.io/5-things-docker-rpi/"pi is an ARM")
Sadly this limits the number of images I can work with right out of the box because I can only run images with arm architecture. [dockerhub armhf](https://hub.docker.com/r/armhf/ "armhf")
  
