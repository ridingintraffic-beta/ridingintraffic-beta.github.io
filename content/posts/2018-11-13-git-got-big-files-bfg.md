---
layout: single
title: 2018-11-13 git-bfg-cleaner
modified:
categories: blog
date: 2018-11-13T08:10:50-04:00
---
# Git got big files or keys? Break out BFG

Everybody messes up, today's mistake was adding a big file to git before a .gitignore was in place to handle it. As a result, github is rejecting the push, even after "removing" the file from git. The reason is that the file still exists in git(history). Time to clean up the mess, break out BFG and nuke it from orbit. -Sadly this means java is involved, but necessary demons. BFG can be found below, and a java jdk needs to get installed. 
BFG Repo-Cleaner by rtyley
A simpler, faster alternative to git-filter-branch for deleting big files and removing passwords from Git history.
[bfg-repo-cleaner](https://rtyley.github.io/bfg-repo-cleaner/ "bfg-repo-cleaner")
Look at BFG repo-cleaner. Welcome back, hopefully there was some reading involved. BFG repo-cleaner will be used to clean up the big files, this can also be used to clean up sensitive data that someone accidentally added to a repo. "cough cough" aws keys. It does this by rewriting the git history and removing all traces of the file. Like many things git sometimes is better not to explain the wizardry and dive right in.
TLDR oh my git… just do this… black magic ensues. 
```
# prework assuming macos
# visit for latest version https://rtyley.github.io/bfg-repo-cleaner/
# creates some folders, downloads bfg symlinks it and paths the file to your bash profile
mkdir -p  /Users/$(whoami)/.local/bin/
curl -o /Users/$(whoami)/.local/bin/bfg-1.13.0.jar http://repo1.maven.org/maven2/com/madgag/bfg/1.13.0/bfg-1.13.0.jar
chmod +x /Users/$(whoami)/.local/bin/bfg-1.13.0.jar
ln -s /Users/$(whoami)/.local/bin/bfg-1.13.0.jar /Users/$(whoami)/.local/bin/bfg
echo $PATH:/Users/$(whoami)/.local/bin/ >> /Users/$(whoami)/.bash_profile
source /Users/$(whoami)/.bash_profile

#commands
git clone http://github.com/bigfiles.git
cd big_files
git gc
cd ../
java -jar bfg --delete-files <myhugefilename> big_files
cd big_files
git reflog expire --expire=now --all
git gc --prune=now --aggressive
# repo is ready to push
```

Welcome back from blindly running commands found on the internet, everything worked correctly right? 
Time to break down what just happened. The prework is setting up BFG and getting it loaded into the environment. 
A folder structure is created in the home folder to store the jar. 
The jar is then downloaded and a symlink is created so that when the new version is added the old symlink can get deleted and reset. 
This is not entirely needed but it certainly helps. 
Next the folder is added to the path env variable in the bash_profile file. 
Then sourcing the bash_profile to use the new path and the new folder. It is not required to do all of this but, let's be honest, this is going to happen more than once and it is better to have this in there for the future. 
After that the repo is cloned ( most likely it already exists so don't worry. 
Then git garbage collection is run. 
Next move out of the directory because BFG needs to be run not in the current dir. 
Fire the BFG passing in the file or wildcard that should be nuked.
Drop back in the folder.
Expire the get reference log which cleans up some things BFG did
Finally git garbage collection to clean up the rest of the cruft.
That's that, files have been removed and all history of them existing has been wiped. This type of process can be especially useful when combined with a git hook and a regex for specific things in files, like keys and whatnot. It can also easily be tied into a Jenkins build pipeline to protect people from themselves. Good luck and when in doubt break out the Big "Friggin" Gun

BONUS: there is a fantastic zine from julia evans that talks about some other great git things
New zine: Oh shit, git!
[new-zine--oh-shit--git-](https://jvns.ca/blog/2018/10/27/new-zine--oh-shit--git-/ "new-zine--oh-shit--git-")

(Links for everything mentioned:)
Removing sensitive data from a repository - User Documentation

[removing-sensitive-data-from-a-repository](https://help.github.com/articles/removing-sensitive-data-from-a-repository/ "removing-sensitive-data-from-a-repository")

BFG Repo-Cleaner by rtyley
[bfg-repo-cleaner](https://rtyley.github.io/bfg-repo-cleaner/ "bfg-repo-cleaner")

Git - git-reflog Documentation
[git-reflog](https://git-scm.com/docs/git-reflog "git-reflog")

Git - git-gc Documentation
[git-gc](https://git-scm.com/docs/git-gc "git-gc")
