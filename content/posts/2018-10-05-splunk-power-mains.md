---
layout: single
title: 2018-10-05 splunk-power-mains
modified:
categories: blog
date: 2018-10-05T08:08:50-04:00
---

# splunking home power
What happens when you take Splunk, a wireless smart meter and an api interface? Splunking your entire home’s power main, without touching a single power wire! This allows a whole house view of all the power being drawn in real time. The only requirements are raspberry pi on a network, a splunk instance somewhere and a usb adapter plugged into the PI.

Hold on, hold on, whats a smart meter? Recently the major power companies have been going around and replacing the old power meters on everybody’s house with a new fancy smart meter. This is essentially just a zigbee enabled power meter than allows power vans to roll down the street and collect all the readings quickly, without having a meter reader go house to house on foot. https://www.comed.com/SmartEnergy/SmartMeterSmartGrid/Pages/ForYourHome.aspx 
This page or the page for the a similar provider can be useful when identifying what kind of meter is installed. Then identify the usb dongle that is compatible to the smart meter, most sites have a list of those devices. Then order it up and get it registered via power company. A simple process however it requires paperwork and some lead time. The device that will be used here is the Rainforest EMU-2 Energy Monitoring Unit. https://www.amazon.com/gp/product/B00BGDPRAI/

Enough of the prework time to dive in. The bulk of the work will be done with a python script that reads from the usb adapter, which is recognized as a serial port on the pi. Then we will install the python script as a service on the pi, which will allow it to run on boot and be easily restarted, and monitored.

The python script: https://gist.githubusercontent.com/ridingintraffic/58cad97660dc2486eba8df306f2af90a/raw/b70ac05348a8f5013458650a85c44190e35de2b4/emu.py 
```
#!/usr/bin/env python
from raven import raven
from raven import _version
import argparse
import logging as log
from splunk_hec_handler import SplunkHecHandler

#splunk hec collector info
token = "<your splunk token>"
splunkhost="<your splunk ip>"

def splunkHec(host, token, logdata):
  logger = log.getLogger('SplunkHecHandlerExample')
  logger.setLevel(log.DEBUG)

  stream_handler = log.StreamHandler()
  stream_handler.level = log.DEBUG
  logger.addHandler(stream_handler)

  
  splunk_handler = SplunkHecHandler(host, token, index="default",
                port=8088, proto='http', ssl_verify=False, 
                                   source="power", sourcetype='_json    ')
  logger.addHandler(splunk_handler)
  payload = {}
  payload.update({"index":"default"})
  payload.update({"sourcetype":"_json"})
  payload.update({"source":"power"})
  payload.update({"host":"gate"})
  payload.update({"event":logdata})
  

  #print payload
  logger.debug(payload)


def main():
    parser = argparse.ArgumentParser(prog="raven",
                                     description="Interrogate the RAVEn USB IHD")
    parser.add_argument('--port', '-p',
                        help='Serial port of the USB stick [/dev/ttyUSB0]',
                        default="/dev/ttyUSB0")
    parser.add_argument('--limit', '-l',
                        help='Count of events to consume before stopping [1000]',
                        default=1000)
    parser.add_argument('--version', '-V',
                        action='version',
                        version='%(prog)s {version}'.format(version=_version.__version__))
    args = parser.parse_args()

    raven_usb = raven.Raven("/dev/ttyUSB0")
    print(raven_usb.get_connection_status())
    print(raven_usb.get_summation_delivered())

    # just wait for a while, because the scheduler inside the stick delivers
    # instantaneous demand automatically
    limit = int(vars(args)['limit']) or -1
    while limit < 0 or limit > 0:
        temp=raven_usb.long_poll_result()
        #print(temp)
        splunkHec(splunkhost, token, temp)

        if limit > 0:
            limit -= 1


if __name__ == "__main__":
    main()
    ```
There are two requirements here pyRaven and Splunk_hec_handler, those can be found here:
https://github.com/vavarachen/splunk_hec_handler 
https://github.com/nonspecialist/pyraven 
The splunk hec handler can also be found in pypi, however at the time of writing the version on pypi was broken and would not pip install, go after the source instead, then do the python setup.py install

Great we have the requirements and the script. The script is pretty straight forward it connects to the serial port, it will listen to the serial firehose and parse when the data flows across. This is where the usb device is just terrible it has options to query the api but then there is a 4 second lag, during which time the same data will spit out automatically and then again from the query. It is really easier to sit there and listen to the firehose. Regardless the pyraven library does all the work and we are plugging in to it to grab the json object and pass it to a splunkhec function. The splunkhec function receives the data and appends the needed wrapper and posts the event. The posting of the event is then handled by the splunkhec library because why reinvent the wheel and deal with rest posts when we can use a library to do it. What this gives us is a nice json object being loaded into splunk.

After we examine the data we can see that a simple splunk search, will give us the graph below.

host=gate | timechart avg(event.raw_demand) as Load span=1m
https://gist.githubusercontent.com/ridingintraffic/df6bf2f9b3a6e5c036402e69da8321bf/raw/4548814ecc46943b91fdfb83f8afec210938ca32/gistfile1.txt

```
{	
  	 event:	{	
    	 demand:	 0.882	
    	 divisor:	 1000	
    	 multiplier:	 1	
    	 raw_demand:	 882	
    	 timestamp:	 2018-10-04T23:30:49Z	
  	}	
  	 host:	 gate	
  	 index:	 default	
  	 log_level:	 DEBUG	
  	 source:	 power	
  	 sourcetype:	 _json	
}
```
#ugly raw text
```
{"event": {"demand": 0.882, "divisor": 1000, "multiplier": 1, "raw_demand": 882, "timestamp": "2018-10-04T23:30:49Z"}, "host": "gate", "index": "default", "log_level": "DEBUG", "source": "power", "sourcetype": "_json"}
```



The service: In order for the pi to run this script automagically a service template is used and then installed. The service is assuming the the emu.py from above is going to be store in /home/pi/ folder.
https://gist.githubusercontent.com/ridingintraffic/b860042351c2a45c84046442f5d5474a/raw/044e1c730df5a748b204223651b64de6a1702399/emu.service

```
[Unit]
Description=emu power
Wants=network-online.target
After=network-online.target
AssertFileIsExecutable=/home/pi/emu.py

[Service]
WorkingDirectory=/home/pi

User=pi
Group=pi

PermissionsStartOnly=true


ExecStart=/usr/bin/python /home/pi/emu.py

# Let systemd restart this service only if it has ended with the clean exit code or signal.
Restart=on-success

StandardOutput=journal
StandardError=inherit

# Specifies the maximum file descriptor number that can be opened by this process
LimitNOFILE=65536

# Disable timeout logic and wait until process is stopped
TimeoutStopSec=0

# SIGTERM signal is used to stop Minio
KillSignal=SIGTERM

SendSIGKILL=no

SuccessExitStatus=0

[Install]
WantedBy=multi-user.target

# Built for ${project.name}-${project.version} (${project.name})
```


Alright the next step is to make sure the python script is executable and then install the service template and get it running. That can all be done with the snippet below.
https://gist.githubusercontent.com/ridingintraffic/7224fe95ab50462dc21f547ca8349d67/raw/08f6c00f0be554b38052053488e518e15e79887c/snippet
```chmod +x /home/pi/emu.py           # setting the python script to executable
cp emu.service /etc/systemd/system # copy the service template
sudo systemctl daemon-reload       # systemd refresh the system folder for templates
sudo systemctl enable emu.service  # install and enable the service
sudo systemctl start emu.service   # start it up!
sudo systemctl status emu          # did it work?      
```

Thats all there is to it, the service is running the python script and outputting a whole bunch of nice data to play with. Once the data is acquired then the possibilities are endless for monitoring and reporting out of splunk for high usage or anomalous usage.

***BONUS:*** (JQ is required for parsing) In order to get the data in to splunk a HTTP collector token is needed. Use curl to generate a token:
https://gist.githubusercontent.com/ridingintraffic/ba53e9a7690162281cd3add568aaffd6/raw/fe63ce8ff0a852cf90ab1e4be1624cc72da6f562/curl%20http%20collector
```
$ curl -u admin:changme https://<yoursplunkIP>:8089/servicesNS/nobody/system/data/inputs/http/?output_mode=json \
-d name=test_thing2  \
-d sourcetype=testing \
-d index=default \
--insecure -X POST |  jq .entry[0].content.token -r

$ 38985917-f6c1-xxxx-xxxx-2c4e8c77798d
```
