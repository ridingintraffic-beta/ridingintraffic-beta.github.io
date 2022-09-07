---
layout: single
title: 2017-03-02 docker, bash, coffee
modified:
categories: blog
excerpt:
tags : [docker, bash, coffee]
image:
  feature:
date: 2017-03-02T08:08:50-04:00


---
# docker reboot
Every once and a while for prem docker installation a reboot is needed.  There are some tools out there that can most likely do this, but today a quick and dirty bash script solved the problem.

Step one dump all the running container IDS, today there were about 23 containers running.
`docker ps -q >>ids`

Next reboot `sudo reboot` 
Finally the bash script to quickly spin them all back up. It reads the source file that was created in the earlier step and does a simple `docker start <id> `  and done.

```
#!/bin/bash
input="ids"
while IFS= read -r var
do
  docker start "$var"
done < "$input"
```

# coffee roasting
Successfully roasted coffee tonight.  It only took 10 iterations in order to get it right.  This was done on my heavily modified popcorn popper with full manual control for heat and blower.  I did a quick blog post write up on medium about it   [successfully-roasting-your-own-coffee](https://medium.com/@michael.j.fettis/successfully-roasting-your-own-coffee-91a3b54bb5bc#.ekb1h4ux9 "successfully-roasting-your-own-coffee")
