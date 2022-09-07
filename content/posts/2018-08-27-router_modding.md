---
layout: single
title: 2018-08-27 router modding
modified:
categories: blog
date: 2018-08-27T08:08:50-04:00
---

# router hacking

These instructions are only intended for the purpose of being able to install legally licensed firmware on routers you own. Do not use this guide as instructions for violating any patents or copyright laws

There is a router out there known as T-Mobile (AC-1900) which is a rebranded ASUS AC1900/68u. The tmobile router sells for $70 and the asus router sells for $130–$170. Whats the difference between these two? Nothing other than firmwares.

Modding firmwares can be a bit scary and intimidating at first. But after that first brick and then that first time bringing it back from the dead, one can gain confidence. Take your time read through the instructions a few times and don't forget to breathe. 

Fundamentals: routers usually run a very basic set of software. This is usually booted up the same way every time a router starts. Most cases we are going to interrupt the router boot and drop it into a bootloader or recovery mode. This is running a separate start option that is usually stored on a separate memory chip or a special set of memory addresses. This allows us to modify the system firmware from outside the usual run process. Various routers have different ways of entering this mode, but at the end of the day its all the same. It's separate environment and generally just the absolute barebones the work with. 

Lets begin, the first pass of the firmware mod. https://www.bayareatechpros.com/ac1900-to-ac68u/ Give this a read and then we can come back here. 

Pretty striaght forward drop router into recovery mode load an older TM firmware. Then modify the cfe/MTD… . wait whats the mtd and cfe? http://www.linux-mtd.infradead.org/doc/general.html "MTD subsystem (stands for Memory Technology Devices) it provides an abstraction layer for raw flash devices. It makes it possible to use the same API when working with different flash types and technologies… provide I/O access to the raw flash. They support a number of ioctl calls for erasing eraseblocks, marking them as bad or checking if an eraseblock is bad, getting information about MTD devices, etc." CFE: common firmware environment. https://en.wikipedia.org/wiki/Common_Firmware_Environment "It is intended to be a flexible toolkit of CPU initialization and bootstrap code for use on embedded processors (typically running on MIPS32/64 instruction set CPUs found in Broadcom SoCs). It is roughly analogous to the BIOS' " . Next we are modifying the CFE so that the proper mac address range is used instead of the TM version. This allows the vanilla asus firmware to get installed because there are restrictions on the firmware only running on a specific range of mac addresses. 

The modded firmware is up and running, bam done great. OOPS we upgraded the router and it reverted back to TM locked down firmware. This is often done by accident, such as not paying attention and thinking "hey I should upgrade". Nuts, they also disabled telnet and ssh in the firmware itself. Not just hiding it in the UI via hidden and disable tags. Looks like they are starting to try pretty hard to stop us from changing the firmware. Not sure why exactly they are trying so hard to lock us out, but alright. 

How do we get at a terminal for the router now? Well, there is a network tools page, that is essentially an open terminal. I guess they assume if you are logged in to the router they should allow anything to happen. Another situation where, "well if they get past my firewall than my internal network is pwned situation." (sigh) Sadly this is often repeated all throughout the tech industry.

After a bit of searching I came across this google doc, that is going to help explain how we will actually hack our router. https://docs.google.com/document/d/1NsZMONmJ70zMmoAKKQJXbTVKytaPJptWTpqih1TD5n8/edit?usp=sharing I am not going to go through and repeat everything that was laid out there but I will skip to some interesting bits. 

First we will need to get the needed files onto a usb. once we have them all zipped up we will plug in the usb drive in to the back of the router and leverage the usb support built in. Then using the network tools windows that we talked about earlier we are going to exploit a code flaw of the form. Thus actually hacking our router by closing the network command and then running our commands. 

If we open the console part of the developer tools on say chrome once we are on the page. we can do something like this. 

```
validForm = function(){document.form.SystemCmd.value = "ping\nmount -t tmpfs tmpfs userRpm";return true;}
```
Here we had the network tools run the ping command and then just drop to a new line and since we had a web shell we can run a mount command and get that usb mounted. Then there are a few more commands that need to be run which takes us to restarting a service on the router. This is done much the same way we would do it on a linux box. 

```
validForm = function(){document.form.SystemCmd.value = "ping\nservice restart_httpd";return true;}
```
Again we are using the ping command then then restarting the httpd service that we slightly tweaked to do our bidding. Then once the service is restarted we do a few more mount commands and a find in order to snag a couple more files from the mounted usb. Finally we run the big scary command that should most definitely not be allowed to be run form a webshell but hey they left the ability there so we are going to take advantage of it . ¯\_(ツ)_/¯

```
validForm = function(){document.form.SystemCmd.value = "ping\nmtd-write2 FW_RT_AC68U_30043763626.trx linux";return true;}
```
Here we are rewriting the firmware to one that we seeded. oops/awesome!
Well then there are a few more cleanup steps involved in finishing up the mod and getting things setup to maintain the firmware. The real interesting thought is that perhaps a router manufacturer should spend less time and effort trying to stop modders from messing with the hardware on site, and a little bit more effort securing the software itself, because if you can do this from the gui, you better believe that there are underlying security vulnerabilities in there that would let worse things happen. It would be a somewhat simple fix of elevated run levels for specific actions and perhaps validating the inputs of the webform/shell to not allow extra commands to flow through… but hey let waste our time where the money is right? Sadly just another router manufacturer that jams out some hardware and leaves a squishy underbelly of software. But hey if no one looks then no one will find the problem right? I hope everyone enjoyed a whirlwind tour of router hacking 101–103, until next time. Same hack time same hack channel. 

Footnotes:
modding tutorial for older firmware tmobile: https://www.bayareatechpros.com/ac1900-to-ac68u/
hardware: https://www.amazon.com/T-Mobile-Wireless-AC1900-Dual-Band-AiProtection-Complete/dp/B01MYTAURW 
google doc with in-depth breakdown
https://docs.google.com/document/d/1NsZMONmJ70zMmoAKKQJXbTVKytaPJptWTpqih1TD5n8/edit?usp=sharing