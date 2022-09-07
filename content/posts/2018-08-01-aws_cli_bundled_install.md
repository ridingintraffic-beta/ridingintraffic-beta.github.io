---
layout: single
title: 2018_08_01_aws_cli_bundled_install
categories: daily
date: 2018-08-01 13:12 -0500
---

# quick and dirty install of the aws cli
sometimes you need to install the aws cli and you just need to get it done.
I often use this when I am doing a bash install of it, for some kind of automation.
```
#download it
$ curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"
#unzip it
$ unzip awscli-bundle.zip
#run it
$ sudo ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
#if you dont have  root you can run it here
$ ./awscli-bundle/install -b ~/bin/aws
# then make sure your $PATH is using it
$ export PATH=~/bin:$PATH 
```
thats it done and simple quick install of the aws cli tools
source: https://docs.aws.amazon.com/cli/latest/userguide/awscli-install-bundle.html

# checking if a python module/ library is installed
again something i have found useful for automation is to see if a python library is installed.
quick and simple bash call to get a true false on the package
```
python -c 'import pkgutil; print(1 if pkgutil.find_loader("module") else 0)'
```
Where `module` is what you are looking for. 