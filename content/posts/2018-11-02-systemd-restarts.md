---
layout: single
title: 2018-11-02 systemd-restarts-friday-links
modified:
categories: blog
date: 2018-11-02T08:08:50-04:00
---

# Systemd restart policy
sometimes services die.  sometimes there is not a better option because of the situatuion that you are in, and you just need to wait it out and then restart the service...  I know it isn't ideal and that there should be better ways around having to do this but hey ¯\_(ツ)_/¯ 
```
[Service]
Type=simple
Restart=always
RestartSec=3
ExecStart=/path/to/script
``` 
In my case I needed to wait it out and restart the service 2 hours after it died. because the process was filling up a hdd and the only option was to let the hdd drain, because the downstream process is the bottle neck.   two hours of seconds equals `7200` seconds. yey

# watch 
sometimes watching a command is super useful
lets watch the disk space in MB for root every 30 seconds
```
watch -n 30 free -m /
```

# friday link dump

some programming langauges that are useful if you are in security, while I dont agree that php is a great language there are times that i have had to read it....
[5-best-programming-languages-to-learn-for-cyber-security](https://hackernoon.com/5-best-programming-languages-to-learn-for-cyber-security-be97071919f9 "5-best-programming-languages-to-learn-for-cyber-security")

automating things in the cloud,  just some stuff for automating
[the-4-phases-to-automating-cloud-management](https://disruptops.com/the-4-phases-to-automating-cloud-management/ "the-4-phases-to-automating-cloud-management")

git prompt. I like having my terminal up to date with things because I have most of my files version controlled.  personally i use [bash-git-prompt](https://github.com/magicmonty/bash-git-prompt.git "bash-git-prompt")   but the article at least explains the usefulness of a git prompt 
[why-linux-developers-should-use-gitprompt](https://hackernoon.com/why-linux-developers-should-use-gitprompt-8d654e5b87e1 "why-linux-developers-should-use-gitprompt") 

