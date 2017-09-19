About a month ago, I was setting up a desktop environment on an old eee pc, and in my efforts to save on as many cpu cycles as I could, I decided to try st (simple terminal from suckless tools), in favour of urxvt which I had been using for years.

The main reason I'd kept away from st before was because it is quite basic and it requires patches to add some basic functionality. Also, it doesn't really  allow you to configure much, except if you recompile it.

As it turns out, when I finally decided to give it a try, it wasn't a lot of work to get things going, and to a point where I was happy to finally give it a go as my main terminal application.
It might be worth to note that my definition of "not a lot of work", was a couple of days of playing around with patches and recompiling st with different colour themes and font configurations, even though fonts is actually one of the things you can specify at start time, but it will fall back to the compiled one if it can't find the font you specify or if a character can't be found in the font hierarchy.

In the eee pc, I'm using Debian, as Arch has dropped support for 32 bits architecture, but on my work computer, I am using Arch. Because of this disparity in distributions, it wasn't worth it for me to spend a lot of time trying to create a build package for arch to compile st and install it with 'pacman'.
I still spent some time trying to sort things out and creating a pkg-build that would apply the patches I wanted, plus some custom ones created by my to configure the theme and a few other small details, but my experience with pkg-build is limited and the fact that this work wasn't reusable on the eee pc, made me give up on it.

In the end, I created a  '~/.config/st/config.sh' which I symlinked to the source code folder where I then compile st.

To make st have enough functionality that it could replace urxvt, I didn't need a lot of patches. In the end, I applied the 'alpha' and the 'scrollback' patches. I tried the latest source code of st, but the patches failed to apply, so I ended up downloading the stable version, which is 0.7, and apply the patches specific to that version, which worked flawlessly.

The default transparency and background colour didn't really do it for me, as it defaults to a light background colour which makes the transparency look odd. So I ran 'make', so that a 'config.h' file got created from the patched 'config.def.h'. Then, I copied the file to '~/.config/st/config.h' and replaced the original with a symlink to that file. That way, I could add my configuration to source control. Then I edited it and changed the alpha from 0xAA to 'static const int alpha = 0x99;', the same amount of transparency I was using on urxvt.
Then I needed to add full black as the background. The patch for alpha support, for some reason removed the black definition and replaces defines a lighter background, so I had to add it to the list of colours.
The way st deals with colours is, it defines an array where the colours with index 0 to 15 are the 16 default terminal colours. Then you can define more colours and give them their own index. Note that this doesn't prevent you from using st with 256 ou true colour. So I defined a new index for black, and defined it as the background colour:
[258] = "#000000",

static unsigned int defaultbg = 258;

Now that this is done, I was left with figuring out a theme to replace those 16 base colours. This would dictate the look of my shell.
While trying to create a theme, I stumbled upon http://terminal.sexy which has a few pre-defined themes and also allows you to create your own. It exports to a variety of formats, including .Xresources (urxvt's format), and st's C header file i.e. the colours array in config.h.

In the end I went with this:

(Source code of the relevant bit of the file)

You can see the whole file here, on my dotfiled repository.

Regarding the scrollback patch, it's worth to mention that I didn't install the mouse support patch, as I try to avoid using the mouse. Also, the path is mostly useless as the scrollback is not smooth, and I can't really tell if it jumps bits of the history. To me, in about a month of using st, this is the only issue that bothers me. I've been working around it because I use tmux for all my sessions, but I don't find it ideal and would like to have a easy way to do sane scrollback.