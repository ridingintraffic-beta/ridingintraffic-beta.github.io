---
layout: single
title: 2017-03-01 js, karma, protractor, npm, bash, akamai
modified:
categories: blog
excerpt:
tags : [js, javascript, karma, protractor, npm, bash, akamai]
image:
  feature:
date: 2017-02-28T08:08:50-04:00

---

# javascript and node
No more just adding things to the package.json  HA!
```
To add an entry to your package.json's dependencies:
npm install <package_name> --save
To add an entry to your package.json's devDependencies:
npm install <package_name> --save-dev
```

version numbers and what they actually mean
https://docs.npmjs.com/getting-started/semantic-versioning
```
Patch releases: 1.0 or 1.0.x or ~1.0.4
Minor releases: 1 or 1.x or ^1.0.4
Major releases: * or x
```

# testing
How fast can I fill in all the holes for TDD... not fast enough apparently ;)


## Karma 
[karma-installation](http://karma-runner.github.io/0.12/intro/installation.html "karma-installation")
[karma-runner](http://karma-runner.github.io/1.0/index.html "karma-runner")



## protractor 
[protractor](https://github.com/angular/protractor "protractor")
There is some youtue stuff on this about protractor and such. [youtube playlist](https://www.youtube.com/playlist?list=PL_noPv5wmuO-mnEq8Sf0h43QRk-Gk2Cvb "youtube playlist")

# npm
.npmrc files are fun to mess with especially when Hopping between a private registry and the public one. Need to remeber to switch them back and forth if the packages in question are not on either one. 

# akamai
There are a couple tools that are in house that lets me look up the akamai staging IP address for our hosts. But if you want to check akamai caching really easy you can do it with the script below.  This is pretty easy for staging or for main property. This also assumes that you have host variable setup in the script... I am going to work on building out all of this in either js or bash because the discovery tools are in node but this request tool is in bash.  I threw it together in about 30 min, It's going to be hackey. 


`script.sh /someurl/ `  

```
#do some magic to get the staging IP 

url=$1
stagingip=MAGIC
host=moremagic

XCheckCacheable=$(curl $fullurl -sI  -H "Pragma: akamai-x-check-cacheable" -H "Host: $host"| tr -d '\r' | sed -En 's/^X-Check-Cacheable: (.*)/\1/p');


if [[ "$XCheckCacheable" =~ "YES" ]]; then
	echo "url is cacheable"
else 
	echo "url is not caching"
fi

Xcache=$(curl $fullurl -sI  -H "Pragma: akamai-x-cache-on" -H "Host: $host"| tr -d '\r' | sed -En 's/^X-Cache: (.*)/\1/p');
#echo $Xcache

if [[ "$Xcache" =~ "TCP_MEM_HIT" ]]; then
	echo "TCP_MEM_HIT"
else 
	echo "not hit"
fi

```

```
$ check-akamai /videos/
url is cacheable
TCP_MEM_HIT
```
