---
layout: post
title: I want my Github Flavored Markdown
author: Andrzej Jóźwiak
tags: jekyll github-pages markdown
date: 29-06-2014
disqus: true
---

As I wanted to move the blog to [github-pages](https://pages.github.com/) I also wanted to use the same type of [markdown](http://daringfireball.net/projects/markdown/) that github uses for its pages, project pages, wikis etc. Believe me at first I thought: *"if I use jekyll surely I will have the same markdown as github"*, and of course I couldn't be more wrong.

So what exactly is [Github Flavored Markdown](https://help.github.com/articles/github-flavored-markdown), often called GFM? Its just normal markdown but with additional capabilities:

1. **Multiple underscores in words** - word between two underscores `_` won't be changed into italics.
2. **URL autolinking** - all URLs will changed to links automatically without additional keywords in markdown
3. **Strikethrough** - its possible to do strike through with special syntax
4. **Fenced code blocks** - you don't have to use `liquid` syntax to create a code block
5. **Syntax highlighting** - you can add the name of programming language next to your code block fence to have nice highlighting
6. **Tables** - easy way to create tables with `-` and `|`

If you are looking for examples of those capabilities you need to visit their wiki [page](https://help.github.com/articles/github-flavored-markdown).

You can imagine that I wanted that and I also wanted to have a `table of contents` which happened to be the most troublesome thing in markdown. By a `table of contents` or `TOC` I mean auto generated links to headings like `<h1>` or `<h2>` in the article.

Jekyll supports several markdown "*interpreters*", I've tried those:

- [kramdown](kramdown.gettalong.org/)
- [redcarpet](https://github.com/vmg/redcarpet)
- [rdiscount](https://github.com/davidfstr/rdiscount)

There is also one called `maruku` but is currently deprecated and you can read on `github-pages` to migrate from using it.

I've tried those `kramdown` settings:

```
# Build settings
markdown: kramdown

kramdown:
  auto_ids: true
  input: GFM
  toc_levels: 1..6
  smart_quotes: lsquo,rsquo,ldquo,rdquo
  use_coderay: false
```

The `input` option is set to `GFM` which was supposed to turn on Github Flavored Markdown, unfortunately it didn't or lets say it did this partially. It was missing:

- fenced code blocks
- syntax highlighting in fenced code blocks
- strikethrough
- table of contents

To get syntax highlighting in kramdown a `liquid` notation needs to be used like this: `{{"{% highlight ruby "}}%}`.

Then I tried `rdiscount`:

```
# Build settings
markdown: rdiscount

rdiscount:
  extensions: ["autolink", "footnotes", "generate_toc", "smart"]
  toc_token: "[TOC]"
```

There were few problems:

- fenced code blocks were not working
- syntax highlighting was not working when applied with fenced code blocks
- no footnotes: `[^1]` although promised on the project site 

At least table of contents worked but not without some trouble. Although I added `generate_toc` on the extensions list it didn't appear with the keyword `[TOC]` that github was using. It seems you need to explicitly specify the keyword with the `toc_token` and believe me it was hard to find.

The last one I've checked was `redcarpet`, its supposed to be the best suited for me as `github` is [working](https://github.com/blog/832-rolling-out-the-redcarpet) on it and deploying it on their machines.

```
# Build settings
markdown: redcarpet
markdown_ext:  markdown,mkdown,mkdn,mkd,md

redcarpet:
  extensions: ["tables", "autolink", "strikethrough", "space_after_headers", "with_toc_data", "fenced_code_blocks"]
```

And comparing to the previous two, `redcarpet` was a jackpot. Fenced code blocks were working, syntax highlighting also. Tables were correctly created, links were auto changed into `<a href>`. Everything worked except the mentioned `table of contents`. You probably noticed the `with_toc_data` on the extensions list, the problem is that it doesn't work because its a renderer option. Currently Jekyll doesn't support renderer options thats one thing, second is that `github-pages` don't support custom Jekyll plugins so you can't write anything in ruby yourself to get a TOC.

From all my desired features `TOC` is the one I can live without because I can always create it manually. I hope this article helps you with your blogs.
