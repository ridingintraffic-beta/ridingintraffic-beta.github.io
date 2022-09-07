---
layout: single
title: '2018-01-08 git_clean_push'

---
# git clean  
quick and easy way to delete files that are untracked in a git repo
'git clean -df'
this will delete any untracked files.  It is often useful to do this after you have been doing some work and have a couple temp files that you don't want around anymore.   No more deleting a bunch of junk.  Let git do it for you. 

# git push origin current branch
working with feature branches means that you will need to push up those branches... here is an alias that will push origin the current branch
alias gpox='git push origin `git rev-parse --abbrev-ref HEAD`'
OR
```% git config --global --list | grep push
push.default=current```
OR
```git push origin HEAD```

