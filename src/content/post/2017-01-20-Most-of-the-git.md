---
title: "Git hacks and tricks"
date: 2018-01-20
categories:
- git
tags:
- git
---

1.Edit commit message on git

This command will let you edit the most recent commit message.

```shell
git commit --amend -m "The changed commit messages!"
```

If case you've already pushed your commit to the remote branch then you need to force push the commit with this command:

```shell
git push <remote> <branch> --force
```

2.Undo 'git add' before committing

If there's only one file that needs to be removed:

```shell
git reset <filename>
```

If you want to unstage all uncommitted changes:

```shell
get reset
```

3.Undo your most recent commit:

```shell
git reset --soft HEAD~1
# make changes to your working files as necessary
git add -A .
git commit -c ORIG_HEAD
```

4.Revert your git repo to a previous commit

```shell
git checkout <SHA>
```


If you want to make commits while you're there, just make a new branch:
```shell
git checkout -b old-state <SHA>
```

To go back to the present state, just checkout to the branch you were on previously.

5.Undo a git merge

```shell
git reset --hard <SHA>
```

or

```shell
git reset --hard HEAD~1
```

6.Remove local (untracked) files from current git branch

When you don't want them to show up every time you use 'git status'.

```shell
git clean -f -n (what files will be removed if you run it)
git clean -f
git clean -fd (if you want to remove directories)
git clean -fX (remove ignored files)
git clean -fx (remove both ignored and non-ignored file)
```

7.Delete a git branch from both locally and remotely

```shell
git branch --delete --force <branch_name>

# OR use -D as short-hand:
git branch -D
```

To delete a remote branch:

```shell
git push origin --delete <branch_name>
```
