---
title: 'pythonic list find'
draft: false
categories: [ blog ]
tags: [ python ]
date: 2019-06-06-T08:08:50-04:00
---

# finding an item in a list
python can do a lot of smart things, lets use the python way of finding items in a list.



```
#python 3
tags=[
      {
        "Key": "CreationDate",
        "Value": "201904011158"
      },
      {
        "Key": "Name",
        "Value": "mymagicserver"
      },
      {
        "Key": "aws:autoscaling:groupName",
        "Value": "my-rad-asg"
      },
      {
        "Key": "Environment",
        "Value": "cloudy"
      },
      {
        "Key": "name",
        "Value": "john"
      },
      {
        "Key": "Department",
        "Value": "sporting goods"
      },
      {
        "Key": "Auto-Off",
        "Value": "no"
      }
    ]

data = list(filter(lambda x: x["Key"] == "aws:autoscaling:groupName" , tags))
print(data[0]["Value"])

##brute force way:

def bruteforce(mylist)
  for item in mylist:
    if item["Key"] ==  "aws:autoscaling:groupName":
      return item["Value"]

print(bruteforce(tags))
```