---
layout: single
title: 2017-03-06 git, splunk, lastpass-cli 
modified:
categories: blog
excerpt:
tags : [ git, splunk,  ]
image:
  feature:
date: 2017-03-06T08:08:50-04:00


---
# git
Git should know better and totally commit and add files that I was working on last night.  I left an uncommited file sitting on a laptop at my house when I went to work today...   Rookie mistake. 

for ALL the repos
```
$ git config --global user.name "John Doe"
$ git config --global user.email "john@doe.org"
```
only for oen repo
```
$ git config user.name "John Doe"
$ git config user.email "john@doe.org"
```

# git aliases are cool
add to your .gitconfig
```
[alias]
  # Show verbose output about tags, branches or remotes
    # abbreviations
    st = status -s
    ch = checkout
    co = commit
 ```

 # splunk indexing

```
#****************************************
# BATCH  ("Upload a file" in Splunk Web):
#****************************************

NOTE: Batch should only be used for large archives of historic data. If you want to continuously monitor a directory 
or index small archives, use monitor (see above). Batch reads in the file and indexes it, and then deletes the file 
from the Splunk instance. 

[batch://<path>]
* One time, destructive input of files in <path>.
* For continuous, non-destructive inputs of files, use monitor instead.
```

# lastpass cli
this github repo is pretty rad.  It is a lastpass cli that does not kick you uot of your main session in your borwser.  This helps when concurrent lastpass sessions is needed. 
[lastpass-cli](https://github.com/lastpass/lastpass-cli "lastpass-cli")



# octet for file permissions
hey I like seeing numbers for file permissions  do you?
```
stat -c "%a %n" .ssh/
700 .ssh/
```
