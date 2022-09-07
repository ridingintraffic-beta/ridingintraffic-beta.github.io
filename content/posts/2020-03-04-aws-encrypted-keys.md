---
layout: single
title: '2020-03-04 aws encrypted keys'
modified:
categories: daily
excerpt:
tags : [  ]
image:
  feature:
---
# AWS credential_process
There is a setting in the aws config that allows AWS to source the credentials externally.   This can be super handy if you don't want to store those as plain text things.  
It is called "credential process".  We can use this in conjunction with  native openssl to give you a poor mans encrypted aws keys.  
[https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sourcing-external.html](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sourcing-external.html) 

First we will take the aws credentials and dump them to a temp file this is named key.json  
```
#key.json
{ 
"Version": 1, 
"AccessKeyId": "NOTAREALKEYID, 
"SecretAccessKey": "NOTAREALSECRET
}
```  
Next we are going to create a script called encrypt.sh, this is going to accept the file that is passed to it and then generate an encrypted file.  
```
#encrypt.sh
openssl aes-256-cbc -a -salt -in ${1} -out ${1}.enc -k "password"
```  
Next we need to write a quick script that will decrypt the encrypted blob and just echo it out.  There is a built in on openssl that does just that.  Also a proper shebang at the top of the file is crucial for aws to be able to run the script properly.  
```
#decrypt.sh
#!/bin/bash -eu
#this is gonna take the encrypted file passed to it and decrypt it
openssl aes-256-cbc -d -a -in $1 -k "password"
```  
Lets encrypt the file and prep it.  
```
$ ./encrypt.sh key.json
# creates key.json.enc
```  
Next we are going to setup our aws profile to read the creds from the external process.  This means that we will need to update `~/.aws/config` and pass the full path of the script and the full path of the encrypted blob.  
```
#~/.aws/config
[profile mine-encrypted]
credential_process = /Users/MYUSERNAME/decrypt.sh /Users/MYUSERNAME/key.json.enc
```   
Finally we are going to tell the current session to use my new profile.  
```
export AWS_PROFILE=mine-encrypted
```  
Lastly we will verify the whole process worked.   
```
aws sts get-caller-identity
```
