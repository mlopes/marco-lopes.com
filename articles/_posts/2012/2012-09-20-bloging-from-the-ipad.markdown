---
layout: post
title: "Bloging from the iPad"
date: 2012-09-20 12:05
comments: true
published: false
tags:
    - ideas
---

Recently, in an attempt to write here a bit more often, I decided to set up my blog in a way that would allow me to blog from the iPad.

# Workflow

First thing I had to decided upon, was how I wanted to create posts from the iPad. I wanted to write in the iPad, and publish from the iPad as well. The blog should be versioned using git, and allow me to still post from the computer.

Keeping all of these in mind, I came up with the following workflow:

* There's a central repository containing Octopress, the blog, the theme and all local changes
* I get a local working copy
  * I clone the central repository to my computer, and am now able to blog directly from the computer.
  * I clone the central repository to a computer accessible from anywhere via SSH, and am now able, with little effort, to blog from any platform that has an SSH client.
* Although deployment is made from the "client" repositories, all changes are pushed back to the central repository, and pulled before starting any new change.
* I can create or edit posts, and preview the result of the markdown.

# Required tools

This means there's a few tools we will need to accomplish the expected results.
Let's start with the first requirement of the workflow.

## Central Repository

Although git is distributed and doesn't really have a "central repository", a always accessible and consistent repository is needed in this specific case.

This can easily be achieved by using github or bitbucket. I used a Linode server I've owned for a few years where I have, among other things, a git+gitolite setup.

## Local copies

On a personal computer, this is easy to achieve, git client and ssh should be enough to ensure one can create a clone of the blog and start working.
For the iPad this is not possible, so I created a clone of the "central repository", in a working area, outside the git+gitolite repository manager.
Using an SSH client,
