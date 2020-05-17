---
layout: post
title:  "Git commands Ref"
date:   2020-05-15
excerpt: "List of useful GIT commands"
---

### Get latest code from remote for all branches
`git fetch`

### Rebase current branch on master
`git rebase origin/master`

### Replace a folder or file from master
`git checkout origin/master <filepath>`

### Pull all files from a branch (but not commits)
`git checkout f-ed-up-branch --`
This will pull all the files from the messed up branch and dumps them on a current branch as changes. 

### How to ammend a commit
`git commit --amend` this will amend the previous commit with the current changes. Needs a Force push.

### Git force push
`git push --force`
