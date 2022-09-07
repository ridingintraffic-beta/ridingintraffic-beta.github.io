---
layout: single
title: '2020-03-18 live dangerously'
modified:
categories: daily
excerpt:
tags : [  ]
image:
  feature:
---
# Turn off signed aps in mac  
I don't want the security warning for unsigned apps.   You can do this with this command on a mac.  
`sudo spctl --master-disable`  
This is terrible and prolly shouldn't be done,  but you know sometimes you need to.   
You can also yank things out of quarantine with this   

`xattr -r -d com.apple.quarantine /path/to/dir`  