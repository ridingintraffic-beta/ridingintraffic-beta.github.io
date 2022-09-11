---
date: 2020-03-28T00:00:00-05:00
title: 'bfg clean local'
draft: false
categories: [ daily ]
tags : [ bfg ]
---
# BFG local cleanup
This is a thing that you can do to clean up your local git repo AFTER doing a commit and before pushing to origin.


# PREWORK
1. Download BFG.jar 
   [https://rtyley.github.io/bfg-repo-cleaner/](https://rtyley.github.io/bfg-repo-cleaner/)    
   [https://repo1.maven.org/maven2/com/madgag/bfg/1.13.0/bfg-1.13.0.jar](https://repo1.maven.org/maven2/com/madgag/bfg/1.13.0/bfg-1.13.0.jar)  
2. Create directory structure
    `mkdir -p ~/.binaries/`  
3. Move BFG into place, chmod it   
    `mv bfg-1.13.0.jar ~/.binaries/ && chmod 777 ~/.binaries/bfg-1.13.0.jar`  
4. setup BASH_PROFILE  
    ```
    echo 'alias bfg="java -jar ~/.binaries/bfg-1.13.0.jar"' >> ~/.bash_profile
    echo "export PATH=/Users/$(whoami)/.binaries:$PATH" >> ~/.bash_profile
    ```  
5. source it  `source ~/.bash_profile` 

# Cleaning repo with BFG  
Now what do we do.  
I will create a new folder, and then create a git repo in that folder.  
This would be the same as doing a git clone...   
`mkdir testing-bfg`  
`cd testing-bfg/`  
`git init` 
```
vi README.md
# do things
# do things that are bad
```  
`git add README.md`  
`git co -a -m "I did an inital commit and it is bad"`  
```
git push origin master
*** pre-receive hook fires and stops me from pushing, so the push fails.
OR
I don't push, but the commit is already in my history, I need to clean it.
```

This is how you fix it.   

In order for BFG to run properly you cannot run it from the source of your git repo, you need to be at least out by one folder.  
`cd ../`  

```
vi ~/my-mess
#this file is now located at your home folder as denoted by the ~/   thingy
#my-mess file
# these are the strings that you want out of your repo and commit history
AWS_TOKEN=X1V34
PASSWORD=
```  
Now we have our dirty git repo located at `testing-bfg`.  
We also have a text file with the "things we want to remove from the repo" that is located at `~/my-mess`  
We are going to run the bfg command which is aliased to `bfg` but you could also do this without the alias, `java -jar bfg-1.13.0.jar` 
The `--no-blob-protection` is telling bfg to rewrite my current history in all my branches and make clean.  
```  
bfg --replace-text ~/my-mess testing-bfg/ --no-blob-protection
```  
Now you will have your file in a "modified" state according to git.  
This is because your local file still has the BAD words in it, however your commit history does not. 
Therefore it is considered modified.  
Now, go through and change your local file to fix your shame and do a new commit and move forward.  

`PROFIT`  