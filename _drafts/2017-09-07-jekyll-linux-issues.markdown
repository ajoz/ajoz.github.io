---
layout: post
date: 07-09-2017
author: Andrzej Jóźwiak
title: Another adventure with Jekyll - Linux installation
tags: linux jekyll
disqus: true
---

Start with update:

```
$ sudo apt-get update
```

Check if you have ruby 2.X.X installed, if not install it.

```
$ ruby --version
The program 'ruby' is currently not installed. You can install it by typing:
sudo apt install ruby
```

We need to install few things:

```
$ sudo apt-get install ruby ruby-dev make gcc
Reading package lists... Done
Building dependency tree
```

Now we can install jekyll:

```
$ sudo gem install jekyll bundler
Fetching: public_suffix-3.0.0.gem (100%)
Successfully installed public_suffix-3.0.0
Fetching: addressable-2.5.2.gem (100%)
Successfully installed addressable-2.5.2
Fetching: colorator-1.1.0.gem (100%)
Successfully installed colorator-1.1.0
Fetching: rb-fsevent-0.10.2.gem (100%)
...
Done installing documentation for public_suffix, addressable, colorator, rb-fsevent, ffi, rb-inotify, sass-listen, sass, jekyll-sass-converter, listen, jekyll-watch, kramdown, liquid, mercenary, forwardable-extended, pathutil, rouge, safe_yaml, jekyll after 21 seconds
Fetching: bundler-1.15.4.gem (100%)
Successfully installed bundler-1.15.4
Parsing documentation for bundler-1.15.4
Installing ri documentation for bundler-1.15.4
Done installing documentation for bundler after 4 seconds
20 gems installed
```

Finally we can run jekyll:

```
$ jekyll help
/var/lib/gems/2.3.0/gems/bundler-1.15.4/lib/bundler/spec_set.rb:87:in `block in materialize': Could not find RedCloth-4.2.9 in any of the sources (Bundler::GemNotFound)
	from /var/lib/gems/2.3.0/gems/bundler-1.15.4/lib/bundler/spec_set.rb:81:in `map!'
	from /var/lib/gems/2.3.0/gems/bundler-1.15.4/lib/bundler/spec_set.rb:81:in `materialize'
	from /var/lib/gems/2.3.0/gems/bundler-1.15.4/lib/bundler/definition.rb:159:in `specs'
	from /var/lib/gems/2.3.0/gems/bundler-1.15.4/lib/bundler/definition.rb:218:in `specs_for'
	from /var/lib/gems/2.3.0/gems/bundler-1.15.4/lib/bundler/definition.rb:207:in `requested_specs'
	from /var/lib/gems/2.3.0/gems/bundler-1.15.4/lib/bundler/runtime.rb:109:in `block in definition_method'
	from /var/lib/gems/2.3.0/gems/bundler-1.15.4/lib/bundler/runtime.rb:21:in `setup'
	from /var/lib/gems/2.3.0/gems/bundler-1.15.4/lib/bundler.rb:101:in `setup'
	from /var/lib/gems/2.3.0/gems/jekyll-3.5.2/lib/jekyll/plugin_manager.rb:48:in `require_from_bundler'
	from /var/lib/gems/2.3.0/gems/jekyll-3.5.2/exe/jekyll:9:in `<top (required)>'
	from /usr/local/bin/jekyll:22:in `load'
	from /usr/local/bin/jekyll:22:in `<main>'
```

We can use bundle and update needed gems:

```
$ sudo bundle update
[sudo] password for ajoz:
The latest bundler is 1.16.0.pre.1, but you are currently running 1.15.4.
To update, run `gem install bundler --pre`
Fetching gem metadata from https://rubygems.org/...........
Fetching version metadata from https://rubygems.org/..
Fetching dependency metadata from https://rubygems.org/.
Resolving dependencies...
Using i18n 0.8.6 (was 0.6.11)
Using minitest 5.10.3 (was 5.5.0)
Using thread_safe 0.3.6 (was 0.3.4)
Using public_suffix 2.0.5 (was 1.4.6)
Using bundler 1.15.4
Fetching coffee-script-source 1.11.1 (was 1.8.0)
Installing coffee-script-source 1.11.1 (was 1.8.0)
Using execjs 2.7.0 (was 2.2.2)
Using colorator 1.1.0 (was 0.1)
Using ffi 1.9.18 (was 1.9.6)
Using multipart-post 2.0.0
Using forwardable-extended 2.6.0
Using gemoji 3.0.0 (was 2.1.0)
Using net-dns 0.8.0
Using rb-fsevent 0.10.2 (was 0.9.4)
Using kramdown 1.13.2 (was 1.3.1)
Using liquid 4.0.0 (was 2.6.1)
Using mercenary 0.3.6 (was 0.3.5)
Using rouge 1.11.1
Using safe_yaml 1.0.4
Using mini_portile2 2.2.0
Using jekyll-paginate 1.1.0
Using jekyll-swiss 0.4.0
Using unicode-display_width 1.3.0
Using tzinfo 1.2.3 (was 1.2.2)
Using addressable 2.5.2
Using coffee-script 2.4.1 (was 2.3.0)
Using ethon 0.10.1
Using rb-inotify 0.9.10 (was 0.9.5)
Using faraday 0.13.1
Using pathutil 0.14.0
Fetching nokogiri 1.8.0 (was 1.6.5)
Installing nokogiri 1.8.0 (was 1.6.5) with native extensions
Using terminal-table 1.8.0 (was 1.4.5)
Using activesupport 4.2.8 (was 4.1.8)
Fetching jekyll-coffeescript 1.0.2 (was 1.0.0)
Installing jekyll-coffeescript 1.0.2 (was 1.0.0)
Using typhoeus 0.8.0
Using sass-listen 4.0.0
Using listen 3.0.6 (was 2.8.3)
Using sawyer 0.8.1
Gem::Ext::BuildError: ERROR: Failed to build gem native extension.

    current directory: /var/lib/gems/2.3.0/gems/nokogiri-1.8.0/ext/nokogiri
/usr/bin/ruby2.3 -r ./siteconf20170907-6481-1guhwhu.rb extconf.rb
checking if the C compiler accepts ... yes
Building nokogiri using packaged libraries.
Using mini_portile version 2.2.0
checking for gzdopen() in -lz... no
zlib is missing; necessary for building libxml2
*** extconf.rb failed ***
Could not create Makefile due to some reason, probably lack of necessary
libraries and/or headers.  Check the mkmf.log file for more details.  You may
need configuration options.

Provided configuration options:
	--with-opt-dir
	--without-opt-dir
	--with-opt-include
	--without-opt-include=${opt-dir}/include
	--with-opt-lib
	--without-opt-lib=${opt-dir}/lib
	--with-make-prog
	--without-make-prog
	--srcdir=.
	--curdir
	--ruby=/usr/bin/$(RUBY_BASE_NAME)2.3
	--help
	--clean
	--use-system-libraries
	--enable-static
	--disable-static
	--with-zlib-dir
	--without-zlib-dir
	--with-zlib-include
	--without-zlib-include=${zlib-dir}/include
	--with-zlib-lib
	--without-zlib-lib=${zlib-dir}/lib
	--enable-cross-build
	--disable-cross-build

To see why this extension failed to compile, please check the mkmf.log which can be found here:

  /var/lib/gems/2.3.0/extensions/x86_64-linux/2.3.0/nokogiri-1.8.0/mkmf.log

extconf failed, exit code 1

Gem files will remain installed in /var/lib/gems/2.3.0/gems/nokogiri-1.8.0 for inspection.
Results logged to /var/lib/gems/2.3.0/extensions/x86_64-linux/2.3.0/nokogiri-1.8.0/gem_make.out

An error occurred while installing nokogiri (1.8.0), and Bundler cannot continue.
Make sure that `gem install nokogiri -v '1.8.0'` succeeds before bundling.

In Gemfile:
  github-pages was resolved to 159, which depends on
    jekyll-mentions was resolved to 1.2.0, which depends on
      html-pipeline was resolved to 2.7.0, which depends on
        nokogiri
```

Ooops still a problem let's see /var/lib/gems/2.3.0/extensions/x86_64-linux/2.3.0/nokogiri-1.8.0/mkmf.log

Ok let's install (reinstall) zlib:

```
$ sudo apt-get install --reinstall zlibc zlib1g zlib1g-dev
...
Processing triggers for man-db (2.7.6.1-2) ...
Setting up zlibc (0.9k-4.3) ...
Setting up zlib1g-dev:amd64 (1:1.2.11.dfsg-0ubuntu1) ...
```

Again let's use bundle:

```
$ sudo bundle update
The latest bundler is 1.16.0.pre.2, but you are currently running 1.15.4.
To update, run `gem install bundler --pre`
Fetching gem metadata from https://rubygems.org/...........
Fetching version metadata from https://rubygems.org/..
Fetching dependency metadata from https://rubygems.org/.
Resolving dependencies...
Using i18n 0.8.6 (was 0.6.11)
Using minitest 5.10.3 (was 5.5.0)
...
Bundle updated!
Post-install message from html-pipeline:
-------------------------------------------------
Thank you for installing html-pipeline!
You must bundle Filter gem dependencies.
See html-pipeline README.md for more details.
https://github.com/jch/html-pipeline#dependencies
-------------------------------------------------
```

Wow, maybe now?

```
$ jekyll help
WARN: Unresolved specs during Gem::Specification.reset:
      rb-fsevent (>= 0.9.4, ~> 0.9)
      ffi (< 2, >= 0.5.0)
WARN: Clearing out unresolved specs.
Please report a bug if this causes problems.
/var/lib/gems/2.3.0/gems/bundler-1.15.4/lib/bundler/runtime.rb:317:in `check_for_activated_spec!': You have already activated public_suffix 3.0.0, but your Gemfile requires public_suffix 2.0.5. Prepending `bundle exec` to your command may solve this. (Gem::LoadError)
	from /var/lib/gems/2.3.0/gems/bundler-1.15.4/lib/bundler/runtime.rb:32:in `block in setup'
	from /var/lib/gems/2.3.0/gems/bundler-1.15.4/lib/bundler/runtime.rb:27:in `map'
	from /var/lib/gems/2.3.0/gems/bundler-1.15.4/lib/bundler/runtime.rb:27:in `setup'
	from /var/lib/gems/2.3.0/gems/bundler-1.15.4/lib/bundler.rb:101:in `setup'
	from /var/lib/gems/2.3.0/gems/jekyll-3.5.2/lib/jekyll/plugin_manager.rb:48:in `require_from_bundler'
	from /var/lib/gems/2.3.0/gems/jekyll-3.5.2/exe/jekyll:9:in `<top (required)>'
	from /usr/local/bin/jekyll:22:in `load'
	from /usr/local/bin/jekyll:22:in `<main>'
```

What to do now?

We can do what the error suggested and just run:

```
$ bundle exec jekyll help
```

And this works or we can check our Gemfile.lock and think what we can do? Maybe we need to fix Gemfile also, let's check github-pages instructions [here](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/). It seems that first I need to update my Gemfile to something like this:

```
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
```

Ok so let's run bundle again:

```
$ bundle update
...
Fetching jekyll-github-metadata 2.9.3
...
Installing jekyll-github-metadata 2.9.3
...
Fetching github-pages 160 (was 29)
Installing github-pages 160 (was 29)
Bundle updated!
```

Some our plugins where updated and we got new. Let's check:

```
$ bundle exec jekyll help
```

Runs ok

```
$ jekyll help
```

Fails :( I have several versions of the same gem installed. What to do?

We have now few choices. We can run jekyll with `bundle exec` this would mean running it like:

```
$ bundle exec jekyll serve
```

This way our jekyll instance will get run with the gems listed in Gemfile.lock. Still its quite a long command. We can shorten it up a little and create a shell alias (ofc if you are using a shell that supports it). If your using Bash you can add a single line to your `.bashrc` file:

```bash
alias jekyll='bundle exec jekyll'
```

This way you will be able to call `jekyll` without additional hassle. There is a third option, if your not a ruby developer and you only need it to configure and run jekyll, then you can check which versions of conflicting gems you have and just install (keep) the correct ones.

```
$ sudo gem uninstall public_suffix

Select gem to uninstall:
 1. public_suffix-1.4.6
 2. public_suffix-2.0.5
 3. public_suffix-3.0.0
 4. All versions
> 3
Successfully uninstalled public_suffix-3.0.0
```

After uninstalling public_suffix-3.0.0 I had the same issue with kramdown:

```
You have already activated kramdown 1.14.0, but your Gemfile requires kramdown 1.13.2.
```

```
$ sudo gem uninstall kramdown

Select gem to uninstall:
 1. kramdown-1.3.1
 2. kramdown-1.13.2
 3. kramdown-1.14.0
 4. All versions
> 3
Successfully uninstalled kramdown-1.14.0
```

For me it worked, I can use jekyll on my machine without `bundle` or aliasing anything:

```
$ jekyll help

jekyll 3.5.2 -- Jekyll is a blog-aware, static site generator in Ruby

Usage:

  jekyll <subcommand> [options]
...
```

I hope this will help you ;-)
