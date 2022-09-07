---
layout: single
title: '2020-11-12 bfg -delete'
modified:
categories: daily
excerpt:
tags : [  ]
image:
  feature:
---
# Help! I committed and pushed something dangerous to github 
This happens we are not perfect and from time to time mistakes are made.  Thats ok  we can fix it with bfg
```
git add my-bad-file
git commit -a -m "im dumb"
git push origin master
# I did not mean to do that, I did not mean to do that
# Lets fix it
# i am in the repo folder named my-repo
rm my-bad-file
git commit -a -m "go away"
git push origin master
cd ../
bfg --delete-files my-bad-file my-repo/
# bfg runs things and does commits to the history
# bfg will also remove that filename from all paths so make sure it isn't reused

cd my-repo
git push origin master --force   #we have to force because we have rewritten the history and git complains about the thing we did
```

There we have it, simple enough but it is incredibly dangerous because you are rewriting git history, so be sure that you know what you are deleting
