---
layout: single
title: 2018-11-09 Systemd-restarts-splunk-py
modified:
categories: blog
date: 2018-11-09T08:08:50-04:00
---

# Systemd restarting
A while ago I had a service that would need to be restarted after it had a cool down of about 2 hours...
I had a stanza that stated
```
Restart=always
RestartSec=7200
```
which was cool because it would just autorestart when it died. At the time the restart would work just fine after the cooldown.
The catch that I ran in to was because of that timer, if I logged in and tried to start it when it was in a failed state, the service would hang. The fix for this is to issue the `systemctl stop service` command and the run the `systemctl start service` It was super annoying figuring this out so a good idea to keep track of it for later ;)

```
[Unit]
Description=your desc
Wants=network-online.target
After=network-online.target
AssertFileIsExecutable=/opt/something.py

[Service]
WorkingDirectory=/opt

User=root
Group=root

PermissionsStartOnly=true

EnvironmentFile=-/etc/default/
ExecStartPre=

ExecStart=/bin/python /opt/something.py

# Let systemd restart this service only if it has ended with the clean exit code or signal.
# Restart=on-success

Restart=always
RestartSec=7200
# ^^ 2 hours so the drive has time to recover

StandardOutput=journal
StandardError=inherit
```

# splunk hec python
before i had a splunk hec python bit that would do things to publish info into splunk from python.  This kind of worked but it had a weird bug in it...   that bug is fixed here.


```
#!/usr/bin/env python
import time
import requests
import urllib3
##turns off the warning that is generated below because using self signed ssl cert
urllib3.disable_warnings()

authToken="my token"
splunkhost="localhost"


def splunkHec(host, token, logdata):
  url='https://'+host+':8088/services/collector/event'
  authHeader = {'Authorization': 'Splunk '+token}
  r = requests.post(url, headers=authHeader, json=logdata, verify=False)



def main():
  while True:
  

    payload = {}
    payload.update({"index":"my_index"})
    payload.update({"sourcetype":"mysourcetype"})
    payload.update({"source":"mysource"})
    payload.update({"host":"myhost"})
    payload.update({"event":data})
    splunkHec(splunkhost, authToken, payload)

if __name__ == "__main__":
    main()
```
