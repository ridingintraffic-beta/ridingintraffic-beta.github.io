---
date: 2018-11-38T00:00:00-05:00
draft: false
title: 'virtualbox shared clipboard and ripgrep install'
categories: [ daily ]
tags: [ ]
---

# Virtualbox shared clipboard
When using virtualbox it is pretty important that you are going to want to have a shared clipboard between your machine and the vm.   This can be done for linux/kali linux by doing the following.
```
Start VirtualBox.
Start the host in question.
Once the host has booted, click Devices | Insert Guest Additions CD Image.
Open up a terminal window in the guest.
Mount the CD-ROM with the command sudo mount /dev/cdrom /mnt.
Change into the mounted directory with the command cd /mnt.
Install the Guest Additions package with the command ./VBoxLinuxAdditions.run
Retart the VM, and once it's booted, click Devices | Shared Clipboard | Bidirectional 

```
there you have it quick and easy and now the vm is good to go.


# ripgrep latest
So i am starting to have to flesh out my linux dot files in a way that will allow me to configure my terminal and shell quickly and easily when i am dropping into various VMs and other machines that are often getting wiped out and reimaged all the time... The easiest way to do this is with a whole bunch of dot files and then some bash sripts to configure when once i pull down the repo. One of my tools is ripgrep so i needed to come up with a way to get latest release of it and then install it...
This is one way but I am sure there are others
```
rg_tag=$(curl --silent "https://api.github.com/repos/BurntSushi/ripgrep/releases/latest" |grep '"tag_name":'  | sed -E 's/.*"([^"]+)".*/\1/' )
curl -LO https://github.com/BurntSushi/ripgrep/releases/download/"$rg_tag"/ripgrep_"$rg_tag"_amd64.deb
sudo dpkg -i ripgrep_"$rg_tag"_amd64.deb
```


https://github.com/BurntSushi/ripgrep/releases/download/0.10.0/ripgrep_0.10.0_amd64.deb