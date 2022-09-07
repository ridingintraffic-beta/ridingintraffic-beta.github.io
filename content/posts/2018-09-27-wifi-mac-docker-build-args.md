---
layout: single
title: 2018_09_27_wifi_things_docker_things
modified:
categories: blog
date: 2018-09-27T08:08:50-04:00
---

# wifi on mac
Sometimes you want to know what wifi network you are on from the terminal or in bash for some reason.
this can be done with airport and then a little bit of awk
```
loc=$(/System/Library/PrivateFrameworks/Apple80211.framework/Versions/Current/Resources/airport -I | awk '/ SSID/ {print substr($0, index($0, $2))}')
echo $loc
```

# docker env variables
There are these things called build arguments in docker.  
Sometimes you will want to pass arguments based on your environment.
An example would be if work uses a proxy and hoe doesnt have that proxy.
```
loc=$(/System/Library/PrivateFrameworks/Apple80211.framework/Versions/Current/Resources/airport -I | awk '/ SSID/ {print substr($0, index($0, $2))}')
if [ "$loc" = "myhomenetwork" ]
then location="home"
docker build --build-arg HTTP_PROXY="" . -t jarvis 
else
    location="work"
    docker build --build-arg HTTP_PROXY="http://WORKPROXY:8080" . -t jarvis 
fi
```