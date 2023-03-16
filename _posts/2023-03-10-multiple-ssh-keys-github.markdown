---
layout: post
date: 10-03-2023
author: Andrzej Jóźwiak
title: Setting up SSH keys for personal and company work on Github
tags: git github ssh linux ubuntu macos
disqus: true
---

In my current company I cannot use my private Github account for working on the comapny's private repositories. I need to have a dedicated account for this case. This is an understandable approach. A company would like to keep a strict access to its organization's resources and avoid any issues with copyrights.

I would like to use different keys for different repositories and avoid using Personal Access Tokens completely.

First a personal key needs to be created. From the command line the `ssh-keygen` needs to be used:

```
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

or in a system that supports an newer algorithm like Ed25519:


```
$ ssh-keygen -t ed25519 -C "your_email@example.com"
```

After running the command it will prompt for two things:

- to enter the path (and filename) for the newly created key
- to enter a passphrase that will be used for the key

I want to distinguish both keys by naming them:
- personalkey
- companykey

An example output when creating a key:

```
Generating public/private rsa key pair.
Enter file in which to save the key (/home/username/.ssh/id_rsa): /home/username/.ssh/personalkey
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/username/.ssh/personalkey
Your public key has been saved in /home/username/.ssh/personalkey.pub
The key fingerprint is:
SHA256:finger print will be here user.name@mail.com
```

Key creation has to be done for both keys, in each case different emails can be used. For personal key a personal email, for company's key a company's email.

Next step is to check if the `ssh-agent` is running:

```
$ eval $(ssh-agent -s)
```

The `-s` option is generating commands. The generated commands look like:

```
SSH_AUTH_SOCK=/tmp/ssh-163sf3GT082P/agent.3234581; export SSH_AUTH_SOCK;
SSH_AGENT_PID=3234591; export SSH_AGENT_PID;
echo Agent pid 3234591;
```

So after using Bash `eval`, `SSH_AUTH_SOCK` and `SSH_AGENT_PID` will be added to the environment variables.

```
$ eval $(ssh-agent -s)
Agent pid 3234591
$ env | grep SSH
SSH_AUTH_SOCK=/tmp/ssh-163sf3GT082P/agent.3234581
SSH_AGENT_PID=3234591
```

The generated keys should all be located in the specified directory, in most cases it will be `~/.ssh/`

```
ls -la ~/.ssh/
total 28
drwx------  2 user user 4096 Mar  5 08:51 .
drwxr-xr-x 55 user user 4096 Mar  9 11:01 ..
-rw-------  1 user user 3454 Dec  9  2021 companykey
-rw-r--r--  1 user user  752 Dec  9  2021 companykey.pub
-rw-r--r--  1 user user 2442 Jan 23 15:11 known_hosts
-rw-------  1 user user 3434 Mar  5 08:47 personalkey
-rw-r--r--  1 user user  751 Mar  5 08:47 personalkey.pub
```

Next step is to add the generated keys to the `ssh-agent`:

```
$ ssh-add ~/.ssh/personalkey
Enter passphrase for /home/username/.ssh/personalkey: 
Identity added: /home/username/.ssh/personalkey (user.name@mail.com)
$ ssh-add ~/.ssh/companykey
Enter passphrase for /home/username/.ssh/companykey: 
Identity added: /home/username/.ssh/companykey (user.name@companymail.com)
```

Each key should be added to corresponding Github accounts. Adding a key is simple.

1. Log into the account
2. Go to `Settings`
3. Go to `SSH and GPG keys`
4. Choose `New SSH key`
5. Add a title (can be anything)
6. Choose key type to be `Authentication Key`
7. Put the public key in the `Key` section
8. Click `Add SSH key` button

Depending on the organization and the company configuring SSO (Single SignOn) might be needed for the added key.

The last step would be to clone a repository and assign a key to it.

```
$ git clone git@github.com:ajoz/ajoz.github.io.git
```

and after it is cloned:

```
$ git config --local core.sshCommand "ssh -i ~/.ssh/personalkey -F /dev/null"
```

Ofcourse `personalkey` should be used for a personal repository, `companykey` should be used for company's repository.