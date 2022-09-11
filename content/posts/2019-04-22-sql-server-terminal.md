---
draft: false
title: 'sql server terminal'
tags: [ ]
categories: [ daily ]
date: 2019-04-22-T08:08:50-04:00
---

# sql server
I needed to connect to a sql db this morning and I didn't have a client.  Docker to the rescue! <br>
`docker run -it mysql /bin/bash` <br>
`mysql -u <myuser> -p -h <myhost> <mydatabase>` <br> 
and done. <br>
when using the `-p` flag it will prompt you for the password instead of having it in the terminal. <br>
Simple easy and connected.

