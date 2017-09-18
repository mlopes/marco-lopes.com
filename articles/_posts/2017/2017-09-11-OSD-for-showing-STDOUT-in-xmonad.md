---
layout: post
title: "OSD to show STDOUT from a command in XMonad"
date: 2017-09-11 22:00
comments: true
published: true
tags:
    - haskell
    - xmonad
    - cli
---

Recently I've been looking a bit into Haskell. This has proven specially
useful since I've been using XMonad as my primary window manager for some time
now. This newlly acquired understanding of Haskell allows me therefore to
go a step further in the configuration of my working environment.

A few days ago, I've written some configuration to map a keybinding to an
action where a command is ran, and then the output of that command is
captured, and shown in a OSD (On Screen Display).

To achieve this, I've used [Dzen](https://github.com/robm/dzen), and the [Dzen
wrapper for
Xmonad](https://hackage.haskell.org/package/xmonad-contrib-0.13/docs/XMonad-Util-Dzen.html).

The first problem I had to deal with, as a newcomer to Haskell, was how to
pass the output of the CLI command, which is an IO action, and therefore not pure,
into the `Dzen` function that triggers the OSD.
The function, `dzenConfig`, will show a OSD with some text, using a specified
configuration, and it has the following signature:

```haskell
dzenConfig :: DzenConfig -> String -> X ()
```

So the type of the first parameter is a `DzenConfig`. This should be quite
straightforward, as, not surprisingly, most of the Dzen configuration
functions return a `DzenConfig`. I wanted the OSD to show up in the current
screen, centered, with a size of 800x30, so that I can show a long...ish
single line of text, and to use a font like terminus.
To sort out this first parameter here's what I've done:

```haskell
import qualified XMonad.Util.Dzen as Dzen

terminus = "-*-terminus-*-*-*-*-24-*-*-*-*-*-*-*"

Dzen.onCurr (Dzen.center 800 30) Dzen.>=> Dzen.font terminus
```

This one is pretty straightforward. I've imported the `Dzen` module using
a qualified import because `font` and `>=>` clashed with functions similarly
named in other namespaces. If you're not too familiarised with Haskell, this
might look a bit weird, but it actually makes sense in the end. I'm going to
try to explain it here. The function `center` has the following signature:

```haskell
center :: Int -> Int -> ScreenId -> DzenConfig
```

This means that it will take 2 Int, one ScreenId and will return a
DzenConfigm but, you'll notice that we're calling it with only the 2 Int parameters.
Because in Haskell all functions are
[curried](https://en.wikipedia.org/wiki/Currying)  by default, when we do
this, we actually get back a function with the signature `ScreenId ->
DzenConfig`. Now, if you look at the signature of `onCurr`:

```haskell
onCurr :: (ScreenId -> DzenConfig) -> DzenConfig
```

You'll see that it takes exactly a function from `ScreenId` to `DzenConfig`,
and returns a `DzenConfig`. So, by partially applying the two Int parameters to
`center`, we get exactly what we need to pass to `onCurr`, and we get out of
it a DzenConfig.

Then, we compose the `DzenConfig` returned by that expression with the
`DzenConfig` we get out of our call to `font`, and we get a composition of our
desired configuration.

Now that that's sorted, let's look at the second parameter. The second paramter,
is of type `String`. This is the string that will be displayed in the OSD.
To get this string, we need to run our command and somehow capture its output.
The way I was able to do this, was by using the `runProcessWithInput`
function, from the package `XMonad.Util.Run`.

The signature for `RunProcessWithInput`, is the following:

```haskell
runProcessWithInput :: MonadIO m => FilePath -> [String] -> String -> m String
```

Now this is... expected, but it kind of causes us a problem. The return value
of our function call is a `MonadIO` of `String`, but our OSD expects a `String`.
For someone with more Haskell experience this would have been straightforward,
but me coming from a non-functional background, my first instinct was to get
the output of the command and try to somehow unpack it so that I could pass
the string into my dzen call. As it turns out, I can't.

My IO action has to be contained, and I can't get my string out of it to use
it in pure functions, so, my call to dzen, had to be moved into my IO function.
So I ended up with this:

```haskell
import qualified XMonad.Util.Dzen as Dzen

import XMonad.Util.Run (runProcessWithInput)

terminus = "-*-terminus-*-*-*-*-24-*-*-*-*-*-*-*"

externalCommandInPopUp :: String -> [String] -> X ()
externalCommandInPopUp c p = do
    s <- runProcessWithInput c p ""
    Dzen.dzenConfig (Dzen.onCurr (Dzen.center 800 30) Dzen.>=> Dzen.font terminus) s
```

To use it, I bind a call to `externalCommandInPopUp` to a key combination, and
specify there which command to run. So, for exemple to have `WinKey+Shift+m` showing
me the song currently playing on `mpd` (music player daemon) using `mpc` (music player client),
I do the following:

```haskell
, ((modMask .|. shiftMask, xK_m),
  (externalCommandInPopUp "mpc" ["current"]))
```

Important to note that the preceding comma in this example is there only to
indicate that this keybinding is one amongst others in a list of keybindings.
Had this be the first one on the list, it wouldn't have the comma. For more
details on how to define `Xmonad` keybindings, refer to the `Xmonad`
documentation.

The full `XMonad` configuration where this example is in use, can be found
[here](https://github.com/mlopes/dotfiles/blob/e12e588257d3938e0e96d1618a98009b9114567a/.xmonad/xmonad.hs).
This will show what I'm talking about in this post, in context.
