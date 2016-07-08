---
layout: post
date: 08-07-2016
author: Andrzej Jóźwiak
title: Github-pages, Jekyll and syntax highlighting
tags: jekyll github-pages markdown
disqus: true
---

After some time I've decided to change the theme on this blog. I used a great theme called brume, I've manually copied the theme to my gh-pages repository and applied all the changes there directly. This approach although fast at first was a bit lacking, I quickly understood I could fork a theme I like, apply needed changes there and use it on my gh-pages branch. I could get all the fixes from the original theme, share my own take on it and keep everything nice, clean and versioned. The only question here was if I want to still use brume?

I decided to use a theme called lagom. One of the changes I've applied on my fork was syntax highlighting.

Article plan:
1) how it was structured
2) problems with css for the syntax, no easy way to change the syntax highlighting color
3) how is syntax highlighted in jekyll (GFM), about rogue, pygments
4) other ways of syntax highlighting: gist, javascript
5) creating a proper css and restructuring everything in my fork
6) line numbers for code
7) how to allow easy code copying (without line numbers)



https://alexpeattie.com/blog/github-style-syntax-highlighting-with-pygments

http://caniuse.com/#feat=css-counters

http://codepen.io/elomatreb/pen/hbgxp?editors=1100

https://gist.github.com/potch/1243028

https://www.sylvaindurand.org/using-css-to-add-line-numbering/

http://stackoverflow.com/questions/826782/css-rule-to-disable-text-selection-highlighting
