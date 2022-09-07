---
layout: single
title: 2018-11-15 hashcat-nvidia-fans
modified:
categories: blog
date: 2018-11-15T08:08:50-04:00
---

# nvidia fan settings
Nvida gpu cards have firmware regulators in place that are OK with the cards running at 85c when they are under load.  This is designed because of the assumption that the cards will only ever be under that kind of load for a short period.  However when you want to run gpu's at 100% utilization for an extended period it is often better to run these cards colder.  Nvidia lets us take control of the fans and override the fan speed.  Lets do that.

 # Headless server
 Odds are the build that we are working with is going to have many cards and it is going to be configured in a headless setup.  This introduces a little bit of a headache, but we can solve this. With an X server a nice xorg config and built in Nvidia settings

``` 
# https://www.shellntel.com/blog/2017/2/8/how-to-build-a-8-gpu-password-cracker
# ^^ this is a pretty good guide but if you want better control then further steps need to be taken.  
# Many of the instlal guides talk about blacklisting the nouveau drivers because they cause all sorts of conflicts.
# blacklist nouveau drivers
# https://www.blackhillsinfosec.com/running-hashcat-on-ubuntu-18-04-server-with-1080ti/

## black list for the win
$ sudo bash -c "echo blacklist nouveau > /etc/modprobe.d/blacklist-nvidia-nouveau.conf"

$ sudo bash -c "echo options nouveau modeset=0 >> /etc/modprobe.d/blacklist-nvidia-nouveau.conf"

$ sudo update-initramfs -u


 ```
 In order to get nvidia settings working we need to have an x environment running 
```
#install lightdm
$ sudo apt-get install --no-install-recommends xorg lightdm

#Disable the X server from auto starting on boot. Otherwise it would cause conflicts when you try to overclock the cards later on.

$ sudo systemctl disable lightdm.service

#install the gtk deps
$ sudo apt-get install libgtk-3-dev -y

```
Next we are going trick the video cards into thinking they have a display plugged in, with nvidia we can do this in software, with amd we need to stick dummy plugs on them.
https://bitcointalk.org/index.php?topic=2432849.0

```
# this generates the xorg config that we will need to have.
# for our system this is stored at /etc/X11/XF86Config

$ sudo nvidia-xconfig -a --allow-empty-initial-configuration --cool-bits=31 --use-display-device="DFP-0" --connected-monitor="DFP-0"
```
The next step is configuring rc.local so that it can be loaded on boot, getting our X env up and running and then spoofing the mointiors plugged in.
```
#rc.local
# You must start the X Server for overclocking and fan control to work
sudo xinit -- /usr/bin/X -config /etc/X11/XF86Config
sleep 10
export DISPLAY=:0
sleep 2
```
Alright now that we have everything up and running we will do this whenever we want to take over the machine and crank up the fans.  Since this machine is up and running 24x7 we don't want to just leave the gpu fans spinning at max.  If we were to do this the fans would eventually burn out and thats no good

```
# GPUFanControlState=1 gives us manual control over the fans, you can do this on a per card basis  with -a [gpu:0]/GPUFanControlState=1 or just all of them this way
# GPUTargetFanSpeed=95 sets the fan to spin at 95%, you can change this to 100 if needed.
$ nvidia-settings -a GPUFanControlState=1 -a GPUTargetFanSpeed=95

# confirm it all worked by viewing fan settings
$ nvidia-settings -q all | grep GPUTargetFanSpeed

```
Alright now everything is blasting the cold air on the cards we can run hashcat or whatever else is going to 

```
reset fan control let firmware handle it
nvidia-settings -a GPUFanControlState=0

nvidia-settings -a [gpu:#]/GPUTargetFanSpeed=95
```