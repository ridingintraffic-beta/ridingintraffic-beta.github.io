---
layout: single
title: '2018-02-26 git is hard '

---

# git
i am bad at git, here is my attempt at rebase and cleaning up
git rebase -i <commit hash>; where the commit hash is the one before yours
the reword all of hte commits except for your latest one, the latest one you pick
then check the git log to make sure that you only have one commit
finally on your feature branch to a `git push --force` which will force your local into the branch you are working on and hopefully everything will get squashed 
