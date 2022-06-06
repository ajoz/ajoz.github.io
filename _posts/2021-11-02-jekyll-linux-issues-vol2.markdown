---
layout: post
date: 02-11-2021
author: Andrzej Jóźwiak
title: Guardians of the Jekyll-axy Vol. 2
tags: jekyll github-pages linux
disqus: true
---

I needed to install Jekyll on a new Ubuntu 20.04.3 LTS, I said to myself "nothing to worry it's not 2017 - it will go smoothly". While saying the words I immediately felt a strong sensation of a deja vu. A sudden chill went down my spine, it all came back in a [flash](http://ajoz.github.io/2017/09/07/jekyll-linux-issues/). Maybe it won't be as bad as before?

So I started by going to the directory containing my blog repo and run `gem install jekyll bundler`. It could be run anywhere but I thought that maybe I will have to do a few things with Gemfile so it will be useful.

```
$ gem install jekyll bundler
Fetching unicode-display_width-1.8.0.gem
Fetching terminal-table-2.0.0.gem
Fetching safe_yaml-1.0.5.gem
Fetching rouge-3.26.1.gem
Fetching forwardable-extended-2.6.0.gem
Fetching liquid-4.0.3.gem
Fetching pathutil-0.16.2.gem

(...)

Installing ri documentation for public_suffix-4.0.6
Parsing documentation for addressable-2.8.0
Installing ri documentation for addressable-2.8.0
Parsing documentation for jekyll-4.2.1
Installing ri documentation for jekyll-4.2.1
Done installing documentation for unicode-display_width, terminal-table, safe_yaml, rouge, forwardable-extended, pathutil, mercenary, liquid, kramdown, kramdown-parser-gfm, ffi, rb-inotify, rb-fsevent, listen, jekyll-watch, sassc, jekyll-sass-converter, concurrent-ruby, i18n, http_parser.rb, eventmachine, em-websocket, colorator, public_suffix, addressable, jekyll after 16 seconds
Fetching bundler-2.2.30.gem
Successfully installed bundler-2.2.30
Parsing documentation for bundler-2.2.30
Installing ri documentation for bundler-2.2.30
Done installing documentation for bundler after 2 seconds
27 gems installed
```

So far so good, too good. Running `jekyll` should show if it is working or not.

```
$ bundle exec jekyll serve
/home/jozwiak/.rvm/rubies/ruby-3.0.2/lib/ruby/3.0.0/rubygems.rb:281:in `find_spec_for_exe': Could not find 'bundler' (1.15.4) required by your /home/jozwiak/Experiments/ajoz.github.io/Gemfile.lock. (Gem::GemNotFoundException)
To update to the latest version installed on your system, run `bundle update --bundler`.
To install the missing version, run `gem install bundler:1.15.4`
	from /home/jozwiak/.rvm/rubies/ruby-3.0.2/lib/ruby/3.0.0/rubygems.rb:300:in `activate_bin_path'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/bin/bundle:23:in `<main>'
```

As I thought, we have multiple clues here:
- Could not find 'bundler' (1.15.4) required by your /home/jozwiak/Experiments/ajoz.github.io/Gemfile.lock
- To update to the latest version installed on your system, run `bundle update --bundler`.
- To install the missing version, run `gem install bundler:1.15.4`

So I can either:
- Go and check what's inside my `Gemfile.lock` and bump it up to the version I have currently installed
- Run `bundle update --bundler` in hopes this will magically fix the issue
- Install the version I have specified in my own `Gemfile.lock`

The correct answer is not on the list but requires a little bit of thinking. After sometime of hiatus from the blog, it is easy to assume that some of my dependencies in the `Gemfile.lock` got outdates and it would be nice to refresh everything. Unfortunately this did not occur to me at that time. So I went with the `bundle update --bundler` option.

```
$ bundle update --bundler
Fetching gem metadata from https://rubygems.org/.........
Using bundler 2.2.30
Using forwardable-extended 2.6.0
Using colorator 1.1.0
Fetching public_suffix 2.0.5
Fetching rb-fsevent 0.10.2
Fetching ffi 1.9.24
Fetching kramdown 1.14.0
Fetching minitest 5.10.3
Fetching execjs 2.7.0
Fetching concurrent-ruby 1.0.5

(...)

Installing jekyll-theme-midnight 0.1.0
Installing jekyll-theme-tactile 0.1.0
Installing jekyll-theme-merlot 0.1.0
Installing jekyll-theme-leap-day 0.1.0
Fetching html-pipeline 2.7.1
Installing html-pipeline 2.7.1
Fetching jekyll-mentions 1.2.0
Fetching jemoji 0.8.1
Installing jemoji 0.8.1
Installing jekyll-mentions 1.2.0
Fetching github-pages 166
Downloading github-pages-166 revealed dependencies not in the API or the lockfile (jekyll (= 3.6.2)).
Either installing with `--full-index` or running `bundle update github-pages` should fix the problem.
```

As one might guess by now doing the spring cleaning in the `Gemfile.lock` is the answer but it still did not occur to me. So I went into failures embrace on an autopilot and went with `bundle update github-pages` first.

```
$ bundle update github-pages
/home/jozwiak/.rvm/rubies/ruby-3.0.2/lib/ruby/3.0.0/rubygems.rb:281:in `find_spec_for_exe': Could not find 'bundler' (1.15.4) required by your /home/jozwiak/Experiments/ajoz.github.io/Gemfile.lock. (Gem::GemNotFoundException)
To update to the latest version installed on your system, run `bundle update --bundler`.
To install the missing version, run `gem install bundler:1.15.4`
	from /home/jozwiak/.rvm/rubies/ruby-3.0.2/lib/ruby/3.0.0/rubygems.rb:300:in `activate_bin_path'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/bin/bundle:23:in `<main>'
```

And as this did not work, I just went with the `--full-index` option.

```
$ bundle update --bundler --full-index
Fetching source index from https://rubygems.org/
Using minitest 5.10.3
Using execjs 2.7.0
Using public_suffix 2.0.5
Using bundler 2.2.30
Using coffee-script-source 1.11.1
Using rb-fsevent 0.10.2
Using kramdown 1.14.0
Using mercenary 0.3.6
Using rouge 2.2.1
Using gemoji 3.0.0

(...)

Using jekyll-theme-leap-day 0.1.0
Using jekyll-theme-merlot 0.1.0
Using jekyll-theme-midnight 0.1.0
Using jekyll-theme-minimal 0.1.0
Using jekyll-theme-modernist 0.1.0
Using jekyll-theme-primer 0.5.2
Using jekyll-theme-slate 0.1.0
Using jekyll-theme-tactile 0.1.0
Using jekyll-theme-time-machine 0.1.0
Fetching github-pages 166
Downloading github-pages-166 revealed dependencies not in the API or the lockfile (jekyll (= 3.6.2)).
Either installing with `--full-index` or running `bundle update github-pages` should fix the problem.
```

So I was at the begining now. This result made me stop and think a bit. I checked my blogs `Gemfile`.

```ruby
source "https://rubygems.org"

gem 'github-pages', group: :jekyll_plugins
```

It did not have any versions specified, I presumed that the latest versions will be used. I deleted the `Gemfile.lock` (ofc I made a backup, just in case) and instead of trying to fix things I run `bundle install` to generate new `Gemfile.lock`.

```
$ bundle install
Fetching gem metadata from https://rubygems.org/...........
Resolving dependencies......
Using bundler 2.2.30
Using coffee-script-source 1.11.1
Using colorator 1.1.0
Using http_parser.rb 0.6.0
Using ffi 1.15.4
Using concurrent-ruby 1.1.9
Fetching faraday-em_synchrony 1.0.0
Fetching unf_ext 0.0.8
(...)

Fetching github-pages 221
Installing github-pages 221
Bundle complete! 1 Gemfile dependency, 99 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed.
Post-install message from dnsruby:

(...)
```

Very proud of my cunningness I thought that now it will work.

```
$ bundle exec jekyll serve
Configuration file: /home/jozwiak/Experiments/ajoz.github.io/_config.yml
            Source: /home/jozwiak/Experiments/ajoz.github.io
       Destination: /home/jozwiak/Experiments/ajoz.github.io/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
                    done in 1.402 seconds.
jekyll 3.9.0 | Error:  no implicit conversion of Hash into Integer
/home/jozwiak/.rvm/gems/ruby-3.0.2/gems/pathutil-0.16.2/lib/pathutil.rb:502:in `read': no implicit conversion of Hash into Integer (TypeError)
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/pathutil-0.16.2/lib/pathutil.rb:502:in `read'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/jekyll-3.9.0/lib/jekyll/utils/platforms.rb:75:in `proc_version'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/jekyll-3.9.0/lib/jekyll/utils/platforms.rb:40:in `bash_on_windows?'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/jekyll-3.9.0/lib/jekyll/commands/build.rb:77:in `watch'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/jekyll-3.9.0/lib/jekyll/commands/build.rb:43:in `process'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/jekyll-3.9.0/lib/jekyll/commands/serve.rb:93:in `block in start'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/jekyll-3.9.0/lib/jekyll/commands/serve.rb:93:in `each'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/jekyll-3.9.0/lib/jekyll/commands/serve.rb:93:in `start'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/jekyll-3.9.0/lib/jekyll/commands/serve.rb:75:in `block (2 levels) in init_with_program'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/mercenary-0.3.6/lib/mercenary/command.rb:220:in `block in execute'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/mercenary-0.3.6/lib/mercenary/command.rb:220:in `each'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/mercenary-0.3.6/lib/mercenary/command.rb:220:in `execute'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/mercenary-0.3.6/lib/mercenary/program.rb:42:in `go'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/mercenary-0.3.6/lib/mercenary.rb:19:in `program'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/jekyll-3.9.0/exe/jekyll:15:in `<top (required)>'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/bin/jekyll:23:in `load'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/bin/jekyll:23:in `<top (required)>'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/bundler-2.2.30/lib/bundler/cli/exec.rb:58:in `load'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/bundler-2.2.30/lib/bundler/cli/exec.rb:58:in `kernel_load'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/bundler-2.2.30/lib/bundler/cli/exec.rb:23:in `run'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/bundler-2.2.30/lib/bundler/cli.rb:478:in `exec'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/bundler-2.2.30/lib/bundler/vendor/thor/lib/thor/command.rb:27:in `run'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/bundler-2.2.30/lib/bundler/vendor/thor/lib/thor/invocation.rb:127:in `invoke_command'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/bundler-2.2.30/lib/bundler/vendor/thor/lib/thor.rb:392:in `dispatch'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/bundler-2.2.30/lib/bundler/cli.rb:31:in `dispatch'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/bundler-2.2.30/lib/bundler/vendor/thor/lib/thor/base.rb:485:in `start'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/bundler-2.2.30/lib/bundler/cli.rb:25:in `start'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/bundler-2.2.30/exe/bundle:49:in `block in <top (required)>'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/bundler-2.2.30/lib/bundler/friendly_errors.rb:103:in `with_friendly_errors'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/gems/bundler-2.2.30/exe/bundle:37:in `<top (required)>'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/bin/bundle:23:in `load'
	from /home/jozwiak/.rvm/gems/ruby-3.0.2/bin/bundle:23:in `<main>'
```

Ok it did not work but not because of gems, but because of a wrong ruby version. The line `jekyll 3.9.0 | Error:  no implicit conversion of Hash into Integer` explains a lot. Although the latest version of jekyll is `4.2.1`, github-pages is using `3.9.0` which does not work with `Ruby 3.x`. Using `Ruby 2.7.x` should fix the problem.

As I am using [rvm](https://rvm.io/) I quickly checked which versions I have available.

```
$ rvm list
   ruby-2.6.3 [ x86_64 ]
=* ruby-3.0.2 [ x86_64 ]

# => - current
# =* - current && default
#  * - default
```

I was using wrong Ruby version. After checking the `rvm list known` for available versions a simple `rvm install ruby 2.7.4` solved the issue and I was able to run jekyll locally.

I hope this helps.