---
layout: single
title: 2018-11-26 fun-with-rsync
modified:
categories: blog
date: 2018-11-26T08:08:50-04:00
---

# fun with rsync
Sometimes you jsut gotta back up files as clones, to another location.  On a mac you have access to rsync and it is avaialbe in homebrew.  `brew install rsync` wait for completion and then you are good to go.  Alright so how about some commands..

# copy from local to usb
`rsync -av /Users/$(whoami)/Documents/ /Volumes/1tb/backup/`

# copy from nas to usb
`rsync -av /Volumes/files/data/ /Volumes/1tb/backup/`

mac will mount nas shares in the share name and then in volumes, so this is reliant on that mount being present when you want to pull something down from said nas.  Not a big deal but it should be noted.
