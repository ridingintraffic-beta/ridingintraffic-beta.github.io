---
layout: single
title: 2018_06_06_ssh_slack_freenas
date: 2018-06-06 13:12 -0500
---

# ssh things
say that you lost your ssh public key but still had a private one....   you can get the public from teh private by running `ssh-keygen -y -f ~/.ssh/id_rsa > ~/.ssh/id_rsa.pub`   
done and simple

# slack annoyer
sometimes you jstu nee dto repeat a message to someone for an extended period of time 

```from time import sleep
from slackclient import SlackClient
import sys

#python junk.py <user> "message" <repeat> <interval seconds>

slack_token = "your token here"
sc = SlackClient(slack_token)
user = sys.argv[1]
user = "@%s" %(user)
message = sys.argv[2]
repeat = int(sys.argv[3])
interval = int(sys.argv[4])
for _ in range(repeat):
 sc.api_call(
  "chat.postMessage",
  channel=user,
  text=message
 )
 sleep(interval)
```


# freenas
want to do paswordless sudo in freenas?


sudo mount -uw /
vi /usr/local/sudoers
user ALL=(ALL) NOPASSWD: ALL
make your changes
mount -ur /
wam bam
