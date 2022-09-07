---
layout: single
title: 2018-10-25 splunk-hec-python
modified:
categories: blog
date: 2018-10-25T08:08:50-04:00
---

# Splunk HTTP collector python script

Hey the http collector is awesome so let use it in python to send things to stuff...
This is going to just take a dictionary as event data and then pass that to the http collector using only the request library .
You will need to have a collector enabled for splunk and a token generated.

```
#!/usr/bin/env python
import time
import requests
import urllib3
##turns off the warning that is generated below because using self signed ssl cert
urllib3.disable_warnings()


authToken="some token"
splunkhost="localhost"


def splunkHec(host, token, logdata):
  url='https://'+host+':8088/services/collector/event'
  authHeader = {'Authorization': 'Splunk '+token}
  r = requests.post(url, headers=authHeader, json=logdata, verify=False)


def main():
  while True:
    
    data = ['kitchen','ballroom','conservatory','dining room','cellar','billiard room','library','lounge','hall','study']
    payload = {}
    payload.update({"index":"default"})
    payload.update({"sourcetype":"_json"})
    payload.update({"source":"status"})
    payload.update({"host":"test"})
    payload.update({"event":data})  
    splunkHec(splunkhost, authToken, payload)

    time.sleep(60)
if __name__ == "__main__":
    main()
```
# enabling splunk http collector from curl
a localhost curl command in order to turn on the http collector
```
curl -k -X "POST" -u admin:<password> https://localhost:8089/servicesNS/admin/splunk_httpinput/data/inputs/http/http/enable
```

# splunk http collector curl
Most time basic terminal tools are the best tools...
this is an example of curl and python to get a https collector token generated and then printed out ... Awesome for when you dont have JQ or any other fancy terminal tools.

```
$ curl -s -u admin:<password> https://localhost:8089/servicesNS/nobody/system/data/inputs/http/?output_mode=json \
-d name=named_token  \
-d sourcetype=cnamed_sourcetype \
-d index=default \
--insecure -X POST | python -c 'import json,sys;obj=json.load(sys.stdin);print obj["entry"][0]["content"]["token"]'
$ 4fcb9bab-8050-4cce-ab18-c76638b8b271
$
```

# deleting http collector from curl
Opps mistakes were made and we need to delete an http collector token from curl
```
curl -k -X "DELETE" -u admin:<password> https://localhost:8089/servicesNS/admin/splunk_httpinput/data/inputs/http/<your_token_name>
```
