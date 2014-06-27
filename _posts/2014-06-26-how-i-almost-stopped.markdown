---
layout: post
date: 26-06-2014
title: How I almost stopped loving SourceTree
tags: git bitbucket sourcetree mercurial github
disqus: true
---

Since my colleague from work showed me [SourceTree](http://www.sourcetreeapp.com/) in which I immediately fell in love with, I used it for every project on Windows. **Yes!** Windows ;-( SourceTree is not available for Linux. On my ElementaryOS / FreeBSD development machines I either use [SmartGit or SmartHg](http://www.syntevo.com/smartgithg/) or simply do everything from console.

If you want to find an alternative to SourceTree that is suitable for your needs take a look at this [site](http://alternativeto.net/software/sourcetree/).

Let's get back to my story. Everything started when I wanted to move my blog to github pages. I changed my previous user name which was `qhunaa` to `ajoz`, so it's more similar to my name and surname. I've added `qhunaa` to SourceTree so long ago, that I forgot how to change it.

The problem occurred when I wanted to make a first push of my new blog. I saw:

![SourceTree uses wrong username]({{site.url}}/assets/2014-06-26-how-i-almost-stopped/wrong_username.png)

So I have started looking:

1. I thought "It's a repository setting", so I went to:

    > Menu > Repository > Repository Settings > Advanced

    Under `User Information` there was a place for `Full name` and `email`. My old user name was there. So I thought the problem will be solved soon. Unfortunately I was wrong.

2. Maybe the user name is kept directly with repository settings:

    > Menu > Repository > Repository Settings > Remotes
    
    Then I've picked my repository and after using `edit` I could change the user name in `Remote details`. Unfortunately I was wrong.

3. It should be in the main app settings:

    > Menu > Tools > Options > General
    
    There is a `Default user information` field there with the user name. Unfortunately I was wrong.

4. SourceTree is probably taking those information from GIT / System. From the command line I used:

    > `git config --global`
    
    And guess what? Unfortunately I was wrong.
    
As I was using SourceTree on Windows, I was really close to reinstalling it (As it's the most common fix on Windows for everything) but I have started digging in settings and I have finally found it:

> Menu > Tools > Options > Authentication > Default User Names

Changing the user name for `github.com` fixed my problem.


