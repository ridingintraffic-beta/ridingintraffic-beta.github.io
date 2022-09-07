---
layout: single
title: 2018_08_02_mac_key_repeat_python_ping	
categories: daily
date: 2018-08-02 13:12 -0500
---

# setting up mac keyboards key repeat
with a new laptop means that you need to make all the changes to all the things.  One of the annoyances that I had was the key repeat.   Yes you can change this in the gui but why would I want to do it there when I can do it in the terminal instead...

```
# read what everything is set at
$ defaults read -g InitialKeyRepeat
$ defaults read -g KeyRepeat
# write some new values to the things.
$ defaults write -g InitialKeyRepeat -int 15
$ defaults write -g KeyRepeat -int 2
```

# python ping 
sometimes you have to do these things
```
#!/usr/bin/env python
import urllib
import threading
import time
import sys

def pinger_urllib(host):
  """
  helper function timing the retrival of index.html
  TODO: should there be a 1MB bogus file?
  """
  t1 = time.time()
  urllib.urlopen(host).read()
  return (time.time() - t1) * 1000.0


def task(m):
  """
  the actual task
  """
  delay = float(pinger_urllib(m))
  print '%-30s %5.0f [ms]' % (m, delay)

# parallelization
tasks = []
URL = 'http://' + sys.argv[1]
t = threading.Thread(target=task, args=(URL,))
t.start()
tasks.append(t)

# synchronization point
for t in tasks:
  t.join()
 ```