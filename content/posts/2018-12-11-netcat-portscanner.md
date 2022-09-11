---
draft: false
title: 'using netcat as a portscanner'
categories: [ daily ]
tags: [ ]
date: 2018-12-11-T08:08:50-04:00
---

# netcat
netcat can work like a portscanner.   If it is the only tool that you have you make do.
this is going to scan localhost across all of the ports

```
nc -zv 127.0.0.1 1-65535
```