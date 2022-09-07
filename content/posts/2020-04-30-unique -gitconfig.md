---
layout: single
title: '2020-04-30 unique .gitconfig'
modified:
categories: daily
excerpt:
tags : [  ]
image:
  feature:
---
# using multiple .gitconfig
There is a neat feature in .gitconfig that will allow you to use uniueq gitconfig depending on the folder structure that you have.   
For example add this to your global gitconfig:
This is assuming that `~/github` has one org of repos, and `~/git` has another, much lke personal and corporate git repos
```
[includeIf "gitdir:~/git/"]
  path = ~/git/.gitconfig
[includeIf "gitdir:~/github/"]
  path = ~/github/.gitconfig
```
Then if you add this is the correpsonding location, the user name/email will be different in each folder structure. 
```
[user]
 name = userid
 email = corporate@email.com
[core]
 hooksPath = ~/git/.git-hooks
 ```
 Thanks to this article in explaining it.	
[setup-git-with-multiple-configs](https://itnext.io/setup-git-with-multiple-configs-9b4111d6928c)