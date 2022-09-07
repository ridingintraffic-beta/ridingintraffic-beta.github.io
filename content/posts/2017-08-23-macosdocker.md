---
layout: single
title: '2017-08-23, macos docker'

---
# Local macos docker file system filled up   
Docker for macos has a bit of a bug that over time this file grows and chews up the availaable storage space that the daemon and containers are allowed to use.  Even if you remove all containers, volumes and images, space usage will persist.  The fix is to simply remove this one file. 
rm ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/Docker.qcow2
and then
restart docker
I found it on docker fourms here: [no-space-left-on-device-error](https://forums.docker.com/t/no-space-left-on-device-error/10894/24 "no-space-left-on-device-error")


# SublimePrettyJson
[SublimePrettyJson](https://github.com/dzhibas/SublimePrettyJson "SublimePrettyJson")
https://github.com/dzhibas/SublimePrettyJson
cmd+ctrl+j to make json blobs pretty....  glorious