---
layout: post
title: "Rvm or no rvm?"
date: 2012-05-14 20:59
comments: true
categories: 
---
What made me write this post, was that today I came across a limitation of using the ruby version managing tool **rvm**. My point is, **rvm** is a very good tool, and an extremely well thought idea. But… only when managing a pure gem system. If any other kind of applications has dependencies on ruby, then using multiple version of ruby can lead to some issues.

So, it all started when I decided to look for a plugin to preview markdown on vim. I quickly came across something called [vim-preview](http://www.vim.org/scripts/script.php?script_id=3344), which should let me do exactly that: preview various documentation formats directly from vim.
Not surprisingly nowadays, the plugin required vim to be compiled with support for ruby plugins. I quickly compiled vim using [homebrew](https://github.com/mxcl/homebrew), and ended up with a vim binary with support for ruby plugins.
Note that, because I use **rvm**, I first made sure that the system version of ruby \(1.8.7\) was selected. The reason for this, is to ensure my vim is linked to the right version of the ruby libraries and the one that should be more widely supported on my system.

After finishing the plugin installation, I changed into my Octopress folder, and used vim to edit a `.md` file. I immediately tried to test the plugin, so I executed:

``` vim
 :PreviewMarkdown
```
 
 And this was the result:
 
``` bash
 Vim: Caught deadly signal SEGV
 Vim: Finished.
 Segmentation fault: 11
```

So why has this happened? The motive is very simple, and yet is the big weakness of *rvm*:

 Octopress requires at least ruby 1.9.2, so when I installed Octopress I used rvm to have ruby 1.9.2 on my computer in parallel with the system version 1.8.7. To avoid having to manually change ruby versions each time I'm working on the blog, I create a `.rvmrc` file with the following contents:

``` bash
rvm use 1.9.2@octopress
```
This basically changes my ruby version to 1.9.2 with the gem set `octopress`.
When vim, which was compiled with ruby 1.8.7, tried to load gems from version 1.9.2, things were not what it was expecting, so it got a segmentation fault.

This is not an **rvm** problem, per se, this is a problem that comes from using different versions of ruby in parallel, that **rvm** fails to address.
One could now discuss if it falls outside **rvm**'s scope to address this kind of issue, or even that it's impossible to control all non-ruby software that in a way or another links to the ruby libraries available on a system on a given moment.

One way or the other, in my opinion, **rvm** is still by far the best platform that I know of, to manage multiple versions of the same language in use, in parallel, in one system.