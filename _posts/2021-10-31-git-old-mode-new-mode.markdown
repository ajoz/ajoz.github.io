---
layout: post
date: 31-10-2021
author: Andrzej Jóźwiak
title: A Git tale of old mode, new mode
tags: git github github-pages file-system
disqus: true
---

Recently I had to move to a new machine. Nothing out of the ordinary you might think. To save some time and work that I had in progress I used my trusty external SSD drive and just copied all my directories containing various projects. I thought everything will be fine but to my great surprise I noticed that almost all of copied projects report changes to the the files tracked by git.

```
$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)

	modified:   .gitignore
	modified:   404.html
	modified:   Gemfile
	modified:   Gemfile.lock
	modified:   LICENSE
	modified:   README.md
	modified:   _config.yml
	modified:   _data/theme.yml
```

I was curious as I didn't change anything recently. Despite the initial surprise I thought about checking the changes, maybe I really did something there. So I run `git diff`, the result was interesting.

```
git diff
diff --git a/.gitignore b/.gitignore
old mode 100644
new mode 100755
diff --git a/404.html b/404.html
old mode 100644
new mode 100755
diff --git a/Gemfile b/Gemfile
old mode 100644
new mode 100755
diff --git a/Gemfile.lock b/Gemfile.lock
old mode 100644
new mode 100755
diff --git a/LICENSE b/LICENSE
old mode 100644
new mode 100755
```

It did not immediately occur to me but `100644` and `100755` are just file permission modes. It seems that the *execution* bit was added everywhere. I had two questions first how to undo it without manual labor and second why did it happen and how to prevent it in the future.

As I am not the sharpest tool in the shed I thought to myself, let's just parse the output of `git status` and pass it `chmod 644`, very crude but will work.

As `git status` spits out things in nice columns this seemed like a job for `awk`. I started with some experimentation.

```
$ git status | awk '{ print $2 }'
branch
branch

to
"git
file:

not
"git
"git
404.html
Gemfile
Gemfile.lock
LICENSE
README.md
```

Unfortunately I got some unwanted things in the output. As the path is preceeded with a single "*modified:*" a simple condition should do the trick.

```
$ git status | awk '{ if ($1 == "modified:") print $2 }'
404.html
Gemfile
Gemfile.lock
LICENSE
README.md
_config.yml
_data/theme.yml
```

Now it was just as simple as passing the output to `xargs`. As I don't want to process empty lines I need `-r` argument and I want only one line per command execution which means I need `-n 1` argument. The complete command looks like this:

```bash

$ git status | awk '{ if ($1 == "modified:") print $2 }' | xargs -r -n 1 chmod 644

```

It looks meh. Maybe we can do something else? Why does Git even care about file privilages?

Git is very smart, it can honor executable bits of the files it keeps the history of. In the docs of [git-config](https://git-scm.com/docs/git-config)

>  core.fileMode
> 
> Tells Git if the executable bit of files in the working tree is to be honored.
> 
> Some filesystems lose the executable bit when a file that is marked as executable is checked out, or checks out a non-executable file with executable bit on. git-clone[1] or git-init[1] probe the filesystem to see if it handles the executable bit correctly and this variable is automatically set as necessary.
> 
> A repository, however, may be on a filesystem that handles the filemode correctly, and this variable is set to true when created, but later may be made accessible from another environment that loses the filemode (e.g. exporting ext4 via CIFS mount, visiting a Cygwin created repository with Git for Windows or Eclipse). In such a case it may be necessary to set this variable to false.

Ok, this explains a lot, initially the repository was created on Ubuntu with an ext4 filesystem but was then copied to an external hard drive with an NTFS. NTFS does not have a notion of such permissions and they are lost when doing the copy. This caused all the problems.

I can use `git config` and just disable the core file mode completely.

```bash

$ git config --global core.filemode false

```

or do it via `~/.gitconfig` file:

```conf

[core]
    filemode = false

```


But do I really want to change it globally? I think this can be done smarter without `git status` and `awk` or changes to `.gitconfig`. Git allows to list all the files with a simple `git ls-files`.

```
$ git ls-files
.gitignore
404.html
Gemfile
Gemfile.lock
LICENSE
README.md
_config.yml
_data/theme.yml
```

Now the `awk` part is not needed anymore. I can just simply create an alias for the command in the `~/.bashrc` file:

```bash
# Fix Git File Permissions
# Usually after copying repo to an external NTFS drive, file permissions are changed
# to 755 (execute bits) from 644, this in turn marks all files as changed in git status
# which causes a lot of confusion and returns output old mode/new mode.
alias fgp="git ls-files | xargs -r -n 1 chmod -x"
```

Now it can be easily run anywhere file permissions are messed by just calling `fgp`. Hope this helps you.