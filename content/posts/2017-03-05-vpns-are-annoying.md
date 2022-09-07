---
layout: single
title: 2017-03-05 vpn, openvpn, fstab, raspberry-pi, vnc
tags:
  - vpn
  - openvpn
  - fstab
  - pi
  - rasberrypi
  - vnc 

---


I missed a day oops, probably had something to do with the up all night post from before.

# VPNs are annoying
For a whole variety of reasons I use vpns for various tasks.   Lately finding one with good throughput has been a headache and annoynce.  For thepast few years I have been using two differnt vpns and both of them have suddenly started to have throughput issues.  

# vpn reviews
 This site looks to offer some good reviewsfor vpns out there   could be worth taking a look  [thatoneprivacysite](https://thatoneprivacysite.net "thatoneprivacysite")


# linux openvpn commands:
use the config files! `sudo openvpn --config <your config>`
add some routes 
```
sudo ip rule add from 192.168.1.111 table 128
sudo ip route add table 128 to 192.168.1.0/24 dev eth0
sudo ip route add table 128 default via 192.168.1.1
```
boom

# raspberry pi 2
turns out the issues that raspberry pi had with network bottleneck on the ethernet seems to be resolved with at least version 2    i can get 10MB a sec throughput 

# reload fstab without rebooting
`mount -a`

# raspbian vnc
ENABLING VNC SERVER
On your Raspberry Pi, run the following commands to make sure you have the latest version of VNC Connect:
```
sudo apt-get update
sudo apt-get install realvnc-vnc-server realvnc-vnc-viewer
```

ENABLING VNC SERVER AT THE COMMAND LINE
You can enable VNC Server at the command line using raspi-config:
```
sudo raspi-config
```
Now, enable VNC Server by doing the following:
Navigate to Advanced Options.
Scroll down and select VNC > Yes.
