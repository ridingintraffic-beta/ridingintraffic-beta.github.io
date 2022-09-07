---
layout: single
title: '2020-10-09 aws cli input and TF state file'
modified:
categories: daily
excerpt:
tags : [  ]
image:
  feature:
---
# AWS cli v2 input prompting
Today I learned that there is an argument that you can pass to the aws cli and it will prompt you for the input for the command that you are trying to run. 
[https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-parameters-prompting.html](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-parameters-prompting.html)  
If you are using version 2 of the aws cli then just add  `--cli-auto-prompt`  to your command and it will make magic.  
This is good if you don't recall what you need to have in a command so it will just ask you along the way.

# Terraform remote state
Lets just say that you are using tf.io and you have remote state stored there.   If you go and do something dumb, and tf-apply using a higher version of terraform than your ci\cd pipeline uses, the ci\cd pipeline will either need to get updated to support the new version or you have a big mess on your hands. Fundamentally all that you need to do is update the tf version inside of your state file and you are good to go.
This is explained a little bit here  [https://support.hashicorp.com/hc/en-us/articles/360001147287-Downgrading-Terraform](https://support.hashicorp.com/hc/en-us/articles/360001147287-Downgrading-Terraform)

This is essentially just changing the version in the state file and then uploading it. 

However remotely hosted state files makes this a little bit harder to do.  There is a great writeup here on how to handle it and step by step for all the proper fields that you will need to fix your shame.  

[https://support.hashicorp.com/hc/en-us/articles/360041299873](https://support.hashicorp.com/hc/en-us/articles/360041299873)
