---
layout: post
title: "First Post"
date: 2012-02-10 10:12
comments: true
categories: "octopress"
author: "Marco Lopes"
---
Just finished setting up Octopress. It took me quite some time, mainly because 
the documentation is not great.
The main problem to me was the instructions on how to get ruby 1.9.2 up and 
running using RVM, as they're simply wrong. After following the instructions on 
the Octopress website I ended up with ruby 1.9.2 but gems would crash with the 
following error:

> ERROR: Loading command: install (LoadError) no such file to load -- zlib

I spent about one hour trying to get this issue fixed without any success. I 
tried compiling ruby with the zlib path set to $rvm_path/usr, I installed zlib 
via RVM and via apt, nothing worked. The first clue to what was going on was 
when I ran:

``` bash bash
$ rvm requirements
```

And found the following:

> To install rbx and/or Ruby 1.9 head (MRI) (eg. 1.9.2-head),
> then you must install and use rvm 1.8.7 first.

Installing ruby 1.8.7, upgrading rubygems with it and then going back to ruby 
1.9.2, fixed the issue.

After that I had no more problems, all the remaning setup went smothly.
I would definetly like to see a pure git deployment strategy.