---
layout: post
title: "First Post"
date: 2012-02-10 10:12
comments: true
tags:
    - octopress
author: "Marco Lopes"
---

**EDIT:** This POST is now very outdated, and this blog is not even running on
Octopress anymore, but in Jekyll. The contents of this article probably don't
reflect how to use Octopress anymore.

Just finished setting up Octopress. It took me quite some time, mainly because 
the documentation is not great. It kind of assumes a level of experience 
installing ruby applications, that I simply don't have.
After following the installation instructions using rvm, on the Octopress 
documentation, I ended up with ruby 1.9.2 but gems would crash with the 
following error:

    > ERROR: Loading command: install (LoadError) no such file to load -- zlib

I spent about one hour trying to get this issue fixed without any success. I 
tried compiling ruby with the zlib path set to `$rvm_path/usr`, I installed zlib via RVM and via apt, re-compiling ruby after that, none of those did the trick.
The first clue to what was going on was when I ran:

``` bash bash
$ rvm requirements
```

And found the following:

    > To install rbx and/or Ruby 1.9 head (MRI) (eg. 1.9.2-head),
    > then you must install and use rvm 1.8.7 first.

I think this is a Linux, or maybe Debian specific issue, which probably is the 
reason for it not to be mentioned on the Octopress docs. Installing ruby 1.8.7, 
upgrading rubygems with it and then going back to ruby 1.9.2, fixed the issue.

After that I had no more problems, all the remaining setup went smoothly.

Since, although I had heard about Octopress before, I decided to give it a try 
after reading the "[Uses Of Git](http://devsundar.github.com/2012/02/09/Uses-of-git/)"
post, I would definitely like to see a pure git deployment strategy, using 
post-update hooks on the server side, or something of the sorts.
