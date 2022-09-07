---
layout: single
title: '2017-10-02 git refresher'

---

# git branching
How about a git refresher moment to remember how to use git.
Different people and places have different ideas behind how source control should be handled.  I have started a new job so I thought that it would be a good idea to refresh my knowledge on git branching strategies.  
Alright we have done a clone of a repo, next step is to do a branch.  Part of that step is doing a checkout of the master 
```
# Checkout the master branch - you want your new branch to come from master
git checkout master

# Create a new branch named newfeature (give your branch its own simple informative name)
git branch newfeature

# Switch to your new branch
git checkout newfeature
```

Great now we have the branch and do some work in the branch.
```
# Fetch upstream master and merge with your repo's master branch
git fetch upstream
git checkout master
git merge upstream/master

# If there were any new commits, rebase your development branch
git checkout newfeature
git rebase master
```
Further cleanup doing some squashing of commits 
```
# Rebase all commits on your development branch
git checkout 
git rebase -i master
```
which opens a text editor and you specify which commits to squash

footnotes: [Chaser324/ce0505fbed06b947d962](https://gist.github.com/Chaser324/ce0505fbed06b947d962 "Chaser324/ce0505fbed06b947d962") 