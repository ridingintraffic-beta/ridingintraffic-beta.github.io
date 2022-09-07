---
layout: single
title: '2017-10-27 telegraf influxdb '

---
# Telegraf 
There is a tool out there called telegraf, which is kind of interesting.  It takes metrics and then forwards them on to a time series database, such as influx. [telegraf getting started](http://docs.influxdata.com/telegraf/v1.4/introduction/getting_started/ "telegraf geting started") This may not seem like that big of a deal, but it can be extra useful, when ingesting metrics like cpu and memory or other simple type metrics, which would require a script to be constantly running. 
There is a nice thing that once you get it configured to run on a server just adding .conf files to the conf.d folder will allow you to add inputs and outputs for everything 

# kafka stuff
kafak is pretty cool too, and it looks like you can run all of kafka out of a docker image if your intent is it use it on a small scale.  Or if you just want to play around with it...  the tricky part is if you try to spin up kafka you are gonna need zookeeper as well. Luckily spotify has a sweet image that handles it.  [docker-kafka](https://github.com/spotify/docker-kafka "docker-kafka")
# Influx db
Influx is the brains behind telegraf. Think of telegraf as just the ingestion method for influx.  Influx uses time series entries for metric in order to udnerstand what the heck is going on.  
A good article on it is here: [influxdb-internals-101-part-one](https://www.influxdata.com/blog/influxdb-internals-101-part-one/ "influxdb-internals-101-part-one") 
There is a nice thing that once you get it configured to run on a server just adding .conf files to the conf.d folder will allow you to add inputs and outputs for everything 
# Git
Oops, a PR was created and you found an error but you don't want to create a new pr...   In steps `git commit --ammend` this will then allow you to add to your last commit, it is as if you meant to do it all along. 

# stitching it all together
The idea with all of these technologies above is to take metrics from A and send them to B.  Kafka will be the broker for everything so you have telegraf do some magic and then write to a kafka topic.  Then you have telegraf reading from a kafka topic and writing into influx db.  Finally you can take influx and use grafana to pull the data out of influx.  Wooo...  data.

