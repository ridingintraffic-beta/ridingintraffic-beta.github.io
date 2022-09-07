---
layout: single
title: '2020-07-08 Terraform external'
modified:
categories: daily
excerpt:
tags : [  ]
image:
  feature:
---
# Fun with terraform

Today there was a fun problem.  We needed to have terraform interrogate the current state and then make a decision based on the current state.   The use case specifically was the number of service instances on an ECS cluster.  If every time a deployment were to happen we would reset the desired count back to the original, then we could lose track of the current number of instances that happened as a result of scaling actions taken based on the current load.  
How do you do this?  
Well the nice thing about our world is there are likely a thousand different ways that we can solve this issue.   Here is how we did it.

We create an "external resource" and then have a local taken the default value and the external value, do a max evaluation on that and output the result.  
Simple enough?  ¯\_(ツ)_/¯ sure why not.  So how do you do it?

# Step 1: the external resouce:
Terraform allows us to have a resource that will get its data from an external script that is accompolished by something like this.
```
data "external" "my-service" {
  program = ["bash", "../scripts/ecs-cluster.sh", "my-cluster", "my-env"]
}
```

We have created a script folder outside of our terraform repo to hold these types of scripts, don't forget to set the script as executable otherwise you'll have a bad time. The script will take 2 arguements to allow the script to be reused across services and environments.
We can then use that resource in a local like this. The example also includes a default value for the service and then our desired count from an already running service.
```
locals {
    service_count= 2
    desired_count   = "${max(local.service_count, "${data.external.my-service.result["desired_count"]}")}" 
    }
``` 

# Step 2: the script  
This is tricky,  is the cluster doesn't exist yet as in the initial deploy then just running aws commands would yield an error and terraform barfs.  In order to resolve this we will need to look at the actual return code from the aws cli command.  When the cli errors it will give a resource not found message but it will also give a return code.  We want to surpress the message and just hold on to the return code.  
You can find information on return codes here [return-codes](https://awscli.amazonaws.com/v2/documentation/api/latest/topic/return-codes.html) For our case the only thing we care about is `NOT 0`  
Finally terraform wants the return from the string as strings and not numbers, the aws cli will return a number, therefore we will "tostring" it to get what we need.  
```
#!/usr/bin/env bash
## ./ecs-cluster.sh  service-name env
service=$1
environment=$2
json=$(aws ecs describe-services --services $service --cluster $service-$environment 2>/dev/null)
if [ $? != 0 ]
then
    echo "{\"desired_count\": \"0\" }"
else
    echo $json | jq -r '{desired_count: .services[0].desiredCount | tostring}'
fi
```

# Profit  
There you have it  terraform can infer state on something already existing and pass that configuration back in to the plan and make sure that state is not lost.   Hurray

