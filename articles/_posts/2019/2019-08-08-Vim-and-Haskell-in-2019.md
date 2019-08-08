---
layout: post
title: Vim and Haskell in 2019
date: 2019-08-08 18:50
comments: true
published: true
summary: Vim is a very powerful editor, here's how it can be set up for programming in Haskell given the state of development of the available tools
image: /assets/posts/images/vim-haskell-lint.png
tags:
    - vim
    - neovim
    - haskell
---

I have recently decided to update my vim configuration for Haskell which had
been set up back in 2017 following the instructions from the "[Vim and Haskell in 2016](http://www.stephendiehl.com/posts/vim_2016.html)"
blog post. I was pleasantly surprised to find out that things seem to have
evolved quite a bit since then, and that Haskell in vim is now pretty feature rich.

## (Neo)Vim

First, let me clear up that I'm not actually using vim, but instead I'm using neovim.
For those not familiar with neovim, it's a fork of vim that had a bit of a
code cleanup and was initially focused on being a version of vim with support
for asynchronous plugins. At this point in time, vim 8 has been out for a while
and the asynchronous plugin support has stopped being an issue in vim, but
there's still some interesting features in neovim, like floating windows,
available at the moment only in the nightly builds, this is going to be
required, in order to get the full functionality that I'll be describing here.

I'm running neovim `0.4.0-dev`, the way to get these nightly builds on your
system varies with which system you're running, instructions for different
systems can be found on 
[neovim's documentation](https://github.com/neovim/neovim/wiki/Installing-Neovim).

## Haskell Tools

The Haskell tooling ecosystem is very rich, and vim plugins make use of those
tools to provide functionality. Here's a list of the tools we want to have
installed, in our path, in order for those plugins to work:

- haskell ide engine (`hie` and `hie-wrapper`) ([installation instructions](https://github.com/haskell/haskell-ide-engine/#installation))
- `hlint` ([installation instructions](https://github.com/ndmitchell/hlint#installing-and-running-hlint))
- apply-refact (`refactor`) ([installation instructions](https://github.com/mpickering/apply-refact#install))
- `hindent` or `stylish-haskell` ([hindent installation instructions](https://github.com/chrisdone/hindent#install), [stylish-haskell installation instructions](https://github.com/jaspervdj/stylish-haskell#installation))

Typically you'll install these tools by running `stack install <tool name>`,
but to be on the safe side, follow the instructions on the tool's
documentation. Haskell Ide Engine specifically, has a slightly different
installation process and `hie-wrapper` won't work correctly unless it is
compiled with the same version of `ghc` as the project you're using it with.

## Vim plugins

Once these tools are up and running, we now need to set up the required vim
plugins to get the most out of vim while editing Haskell projects.
We're going to be using the following plugins:

- `neoclide/coc.nvim`
- `neovimhaskell/haskell-vim`
- `alx741/vim-hindent` or `alx741/vim-stylishask`
- `mpickering/hlint-refactor-vim`

I'm not going to provide specific instructions on how to install each plugin
as those instructions can be found on each plugin's page. The instructions
are also very simple, and will depend on which plugin manager you're using. I
personally prefer to use vim plugger so, assuming I have plugger installed and 
configured, I just add the following to my configuration, then reload the
configuration and run `PlugUpdate`:

```vim
Plug 'neoclide/coc.nvim', {'do': { -> coc#util#install()}}
Plug 'neovimhaskell/haskell-vim'
Plug 'alx741/vim-hindent'
Plug 'mpickering/hlint-refactor-vim'
```

### coc.vim

coc.vim is described as "an intellisense engine for vim8 & neovim", it will
use `hie` as the backend to provide intelligent suggestions, code navigation and
show errors.

You'll need to set up `hie` as the language server, Haskell specific
instructions on how to configure coc.vim can be found
[here](https://github.com/neoclide/coc.nvim/wiki/Language-servers#haskell) and
you can find and example configuration containing useful keybinding for
navigating errors, going to definition, showing documentation/type under the
cursor, etc,
[here](https://github.com/neoclide/coc.nvim#example-vim-configuration).

Bellow is an example of coc.vim's functionality for showing the type under the
cursor, the keybinding, if you use the example configuration linked above,
will be `K`:

![Seeing the type of the expression under the cursor](/assets/posts/images/vim-haskell.png){: .center-image .img-responsive }

As you can see in the screenshot, it gives you not just the declared type but
also the inferred type for the current context.

To also get linting, you'll need to change to `true` the `hlintOn` setting which in
the example configuration is set to `false`. So, in vim do `:CocConfig`, which
will open coc.vim's configuration file, and change the Haskell language server
configuration to:

```json
"haskell": {
    "command": "hie-wrapper",
    "rootPatterns": [".stack.yaml", "cabal.config", "package.yaml"],
    "filetypes": ["hs", "lhs", "haskell"],
    "initializationOptions": {},
    "settings": {
	"languageServerHaskell": {
		"hlintOn": true,
		"maxNumberOfProblems": 10,
		"completionSnippetsOn": true
	}
     }
  }
```

Now you should be able to see linting suggestions, which we should be able to
automatically apply once we install `hlint-refactor-vim`:

![Seeing linting suggestions](/assets/posts/images/vim-haskell-lint.png){: .center-image .img-responsive }


### haskell-vim

haskell-vim gives you a more Haskell and contextually aware syntax highlighting.
It's worth it to be aware that not every theme will play well with this
plugin, but most quality themes will provide a complete colour configuration
and therefore work well with haskell-vim. 

### vim-hindent or vim-stylishask

These plugins use `hindent` and `stylish-haskell` to auto-format the file on
save. That's it really, they just help you stick to a coding style convention.

### hlint-refactor-vim

This plugin uses hlint's refactor functionality to apply the suggestions made
by hlint to the current file, or the code under the cursor. By default `to`
will apply changes to the code under the cursor, and `ta` will apply all the
suggested changes in the file.

### Other plugins

I use a number of other plugins that provide me with common functionality that
is also useful when writing Haskell, but that are not specifically related to
writing Haskell. The list of all plugins I use can be found
[here](https://github.com/mlopes/dotfiles/blob/11c48026bb707937193b03b364b02ac0ad2886ad/.local/share/nvim/site/config/plugins.vim).

## Conclusion

And that's it really, with these plugins, and some basic configuration, I have
smart code completion, smart code navigation, information about the code and 
types, error, linting and automatic refactoring, all without leaving vim.

All in all, Haskell with vim in 2019 is in a pretty good place.


