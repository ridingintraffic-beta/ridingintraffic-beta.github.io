---
date: 2020-05-08T00:00:00-05:00
title: 'playing with ssm'
draft: false
categories: [ daily ]
tags : [ aws ]
---
# ssm rate limiting
I was spending a little bit of time trying to scrape some ssm stuff and i figured out that there seems to be a "feature" built in to ssm that doesn't let you do too many describes.
AWS will complain and say NO BUENO if you do it to often.
```
aws ssm describe-parameters | jq '.Parameters[]| "\(.Name) \(.Type)"'

An error occurred (ThrottlingException) when calling the DescribeParameters operation (reached max retries: 2): Rate exceeded
```

I thought that was kind of a nice thing to stop people from describe everythign too often.  It can be seen as a security feature as well. 

