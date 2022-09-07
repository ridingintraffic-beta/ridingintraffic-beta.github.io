---
layout: single
title: '2018-02-19 git_revert '

---

# git revert
Yey more git.  I am still not the greatest at git, therefore here is the step by step on how to revert a PR/merge from git.

first checkout the master branch:
`Git checkout master`
then run a git log and get the id of the merge commit.
`git log`
then revert to that commit:
`git revert -m 1 <merge-commit>`
With ‘-m 1’ we tell git to revert to the first parent of the mergecommit on the master branch. -m 2 would specify to revert to the first parent on the develop branch where the merge came from initially.

Now commit the revert and push changes to the remote repo and you are done.

Getting back the reverted changes
This changes the data to look like before the merge, but not the history. So it’s not exactly like an undo. If we would merge develop into master again the changes we reverted in master wont be applied. So if we would like these changes back again we could revert our first revert(!).

`git revert <commit-of-first-revert>`