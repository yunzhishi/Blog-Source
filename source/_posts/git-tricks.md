---
title: Git Tricks
date: 2016-05-18 23:03:29
categories:
    - Git
tags:
    - Git
    - Tricks
---

I learned some git tricks from the website: [廖雪峰的官方网站](liaoxuefeng.com). Unfortunately the posts in the website are all writen in Chinese, therefore I would like to share them here in English.

### Git Alias
We use some git commands such as `git status` a lot. It is often annoying to redo hundreds of times because of typos when we type this word `status`.
`git st` is much easiser than `git status`, we like lazy solutions.
All we need to do is typing one command line, telling git that thereafter `st` is the alias of `status`:
``` bash
$ git config --global alias.st status
```
Now just try typing `git st` and see if it works.

<!-- more -->

There are other commands that can be abbreviated, it is commonly used that `co` for `checkout`, `ci` for `commit` and `br` for `branch`:
``` bash
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.br branch
```
Since then, a commit command can be simplified as:
``` bash
$ git ci -m "blah blah ..."
```
In the `git config` commands above, `--global` option means that the parameters are used globally, and are available in all repositories within the same user account in the computer.

Besides, alias is also used for custom git commands. For example, let's set up a `git last` command showing last single commit log:
``` bash
$ git config --global alias.last `log -1`
```
Using `git last` to show the latest commit information:
``` bash
$ git last
commit 6357e65da0faaeb95eb87251ce5b0db6e3e1b7d6
Author: Yunzhi Shi <yzshi08@utexas.edu>
Date:   Tue May 10 11:14:56 2016 -0500

    Seislet test.
```
Behold, well, check this out:
``` bash
$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```
The output of `git lg`:
![git last](/images/git-tricks/git-last.png)
Dang, why didn't I know about this before?

### Alias Config File
While configuring git, attaching `--global` option makes it effective to current user, otherwise it will only be effective within currect repository.
Where do all these configurations go? For each repository, configurations are stored in file `.git/config`:
``` bash
$ cat .git/config
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
	ignorecase = true
	precomposeunicode = true
[remote "origin"]
	url = git@github.com:TCCS-BEG/all.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
[alias]
    last = log -1
```
Current alias configurations are written in `[alias]`.
For global cofigurations, they are stored in an invisible file under main folder `~/.gitconfig`:
``` bash
[user]
	name = Yunzhi Shi
	email = yzshi08@utexas.edu
[alias]
	st = status
	co = checkout
	ci = commit
	br = branch
	unstage = reset HEAD
	last = log -1
	lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
[color]
	ui = true
```
We can directly modify content in this file to configure alias settings or other personal customizations.

### We Are Lazy
We are lazy, so we utilize all we could to minimize our labor XD.
