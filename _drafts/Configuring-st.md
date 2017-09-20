---
layout: post
title: "Configuring st"
comments: true
published: true
tags:
    - st
    - terminal
    - c
---

## Background
About a month ago, I was setting up a desktop environment on an old eee pc,
and in my efforts to save as many cpu cycles as I could, I decided to try
[st](https://st.suckless.org/ "st - simple terminal") (simple terminal from
[suckless](https://suckless.org/ "Suckless - Software that sucks less")), in favour of
[urxvt](http://software.schmorp.de/pkg/rxvt-unicode.html "rxvt-unicode
homepage") which I had been using for a number of years.

The main reason I had kept away from st before was because it is quite basic
and it requires patches in order to add some basic functionality. Also, it
doesn't allow you to configure much, except if you recompile it, and even
then, it's configuration is somewhat limited.

As it turns out, when I finally decided to give it a try, it wasn't a lot of
work to get things to a point where I was happy to try it as my main terminal
emulator application in X.

At this point, it might be worth to note that when I say "not a lot of work",
it still means a couple of days of playing around with patches, recompiling st
with different colour themes and font configurations, and sorting out odds and
ends.

## Setup
Because [Arch dropped 32bit
support](https://www.archlinux.org/news/phasing-out-i686-support/), on the eee
pc I installed Debian. On my other desktop though, I am using Arch. This
disparity in distributions makes it much less worth it to spend a lot of time
trying to create and maintain a PKGBUILD file to use in Arch to patch, compile
and install st using `makepkg` and `pacman`, as it won't help me at all with
the Debian installation.
In spite of this, I still spent some time trying to create a working PKGBUILD,
that would apply the patches I wanted, plus some custom ones created by me to
set up the colour theme, and a few other small details. In the end, it proved
to take more time than what I was willing to spend, and the fact that this
work wasn't reusable on the eee pc, made me give up on this approach.

I ended up going with the simpler solution of just maintaining a configuration
  file in `~/.config/st/config.sh` which I symlink to the source code folder
  which I then use to compile st.

### Patches
To make st have enough functionality that it could replace urxvt, I didn't need a lot of patches. In the end, I applied the `alpha` and the `scrollback` patches. I tried the latest source code of st, but the patches failed to apply, so I ended up downloading the stable version, which is 0.7, and apply the patches specific to that version, which worked flawlessly.

The default transparency and background colour didn't really do it for me, as it defaults to a light background colour which makes the transparency look odd. So I ran `make`, so that a `config.h` file got created from the patched `config.def.h`. Then, I copied the file to `~/.config/st/config.h` and replaced the original with a symlink to that file. That way, I could add my configuration to source control. Then I edited it and changed the alpha from 0xAA to `static const int alpha = 0x99;`, the same amount of transparency I was using on urxvt.
Then I needed to add full black as the background. The patch for alpha support, for some reason removed the black definition and replaces defines a lighter background, so I had to add it to the list of colours.
The way st deals with colours is, it defines an array where the colours with index 0 to 15 are the 16 default terminal colours. Then you can define more colours and give them their own index. Note that this doesn't prevent you from using st with 256 ou true colour. So I defined a new index for black, and defined it as the background colour:
[258] = "#000000",

static unsigned int defaultbg = 258;

Now that this is done, I was left with figuring out a theme to replace those 16 base colours. This would dictate the look of my shell.
While trying to create a theme, I stumbled upon http://terminal.sexy which has a few pre-defined themes and also allows you to create your own. It exports to a variety of formats, including .Xresources (urxvt's format), and st's C header file i.e. the colours array in config.h.

In the end I went with this:

(Source code of the relevant bit of the file)

You can see the whole file here, on my dotfiled repository.

`Add context to this one and go into a bit more detail about fonts`: Even though fonts is actually one of the things you can specify at start time, but it will fall back to the compiled one if it can't find the font you specify or if a character can't be found in the font hierarchy.

Regarding the scrollback patch, it's worth to mention that I didn't install the mouse support patch, as I try to avoid using the mouse. Also, the path is mostly useless as the scrollback is not smooth, and I can't really tell if it jumps bits of the history. To me, in about a month of using st, this is the only issue that bothers me. I've been working around it because I use tmux for all my sessions, but I don't find it ideal and would like to have a easy way to do sane scrollback.
