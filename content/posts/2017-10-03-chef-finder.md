---
layout: single
title: '2017-10-03 chef'

---

# expose hidden files on a mac
Finder hides all sorts of stuff,   let expose that

```
defaults write com.apple.Finder AppleShowAllFiles true
defaults write -g AppleShowAllFiles -bool true
killall Finder
````


# Lets relearn chef

```vagrant box add bento/ubuntu-14.04 --provider=virtualbox
vagrant init bento/ubuntu-14.04
vagrant up
vagrant ssh

sudo apt-get update
sudo apt-get -y install curl

curl https://omnitruck.chef.io/install.sh | sudo bash -s -- -P chefdk -c stable -v 2.0.28
#gets the latest version of chefdk and installs it
```
simple enough now vagrant has chef and we can do chef local type configs and changes.

Lets cleanup the vagrant image and delete it
`vagrant destroy --force` does just that
