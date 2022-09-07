---
layout: single
title: 2018-11-21 book-list_chaos-engineering_docker-python
modified:
categories: blog
date: 2018-11-21T08:08:50-04:00
---

# books
Everyone like to read things right?   well I came across this list from palo alto earlier and it looks pretty promising. 
[cybercanon.paloaltonetworks](https://cybercanon.paloaltonetworks.com/ "cybercanon.paloaltonetworks")  There area  whole bunch of books in there raning from security to chaos engineering.  Down the chaos engineering rabbit hole we go
checkout this list of chaos engineering everything here [awesome-chaos-engineering](https://github.com/dastergon/awesome-chaos-engineering "awesome-chaos-engineering") 

# what is chaos engineering?
It is using the ideas that the systems that we create now are so complex and dense.  We are engineering the methods that will allow for random services and pieces to fail at random intervals.  Then if the systems are resilient enough, they should have self healing or fault tolerance built it.  if that is the case then the overall picture will all the system to function on some level, even when random components and interfaces are brought down unexpectedly.  This is all only possible if the systems themselves are in a measurable and steady state.  If you don't know what is healthy and have a means to measure healthy then it is going to be double tough to measure and understand unhealthy.  Then the second part as stated before is the ability for the systems to self heal and rebuild things on their own. that is the extra complex part. 

# docker and python
I needed to play with a python library and I hate installing things on my laptop and then leaving cruft around.  Using docker we can do a quick and dirty test of software.
Lets pull down a docker image that has python in it real quick.
`docker pull python:3.7.1-stretch`
then we can run it and get a shell
`docker run --rm -it python:3.7.1-stretch /bin/bash`
boom we are in , next quick fix `apt-get update && apt-get install -y vim`
that will update the repos and give us vi so we can start writing stuff.   then the image already has git so go snag the python library that you wanted to mess with pull down the repo then run `python setup.py install` get it configured and we can either build a python script in vim or drop into a python interactive terminal.  Done,  throw away the garbage when your done.
If you needed to save the stuff you were working on you can always volume mount and have your files out of the continer. 