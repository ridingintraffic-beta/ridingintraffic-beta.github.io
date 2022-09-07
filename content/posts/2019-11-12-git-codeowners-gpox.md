---
layout: single
title: 2019-11-12 git-codeowners-gpox
modified:
categories: blog
date: 2019-11-12-T08:08:50-04:00
---

# Some git notes
I like git I user git a lot, I did not know about CODEOWNERS, this is a nice feature.

# automatic PR approver to a repo
Adding codeowners can allow for automatic PR approvers for branches or all things
[github.com about-code-owners](https://help.github.com/en/github/creating-cloning-and-archiving-repositories/about-code-owners)

You can use a CODEOWNERS file to define individuals or teams that are responsible for code in a repository.
To use a CODEOWNERS file, create a new file called CODEOWNERS in the root, docs/, or .github/ directory of the repository, in the branch where you'd like to add the code owners.

# Examples
[about-code-owners#example-of-a-codeowners-file](https://help.github.com/en/github/creating-cloning-and-archiving-repositories/about-code-owners#example-of-a-codeowners-file)
## generic example from github.com
```
# <filename CODEOWNERS>
# This is a comment.
# Each line is a file pattern followed by one or more owners.

# These owners will be the default owners for everything in
# the repo. Unless a later match takes precedence,
# @global-owner1 and @global-owner2 will be requested for
# review when someone opens a pull request.
*       @global-owner1 @global-owner2

# Order is important; the last matching pattern takes the most
# precedence. When someone opens a pull request that only
# modifies JS files, only @js-owner and not the global
# owner(s) will be requested for a review.
*.js    @js-owner
```


# git push origin alias
gpox #short for "git push origin x" much like "git push origin master"

If you add this to your bash profile. IE `~/.bash_profile` you can quickly and easily push to the branch that you are already in.
```
# <filename ~/.bash_profile>
...

alias gpox='git push origin `git rev-parse --abbrev-ref HEAD`'
```

then to use it you would just add a commit and make a push like this
```
git commit -a -m "your message"
gpox 
```
```
✔ ~/git/github/ridingintraffic/ridingintraffic.github.com [master|✔]
13:47 $ git co -a -m "trying to make it more clear"
[master eb4fe26] trying to make it more clear
 1 file changed, 2 insertions(+), 2 deletions(-)
✔~/git/github/ridingintraffic/ridingintraffic.github.com [master ↑·1|✔]
13:48 $ gpox
Counting objects: 3, done.
.....
```