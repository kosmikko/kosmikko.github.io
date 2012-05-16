---
layout: post
title: "OSX ZSH magic"
date: 2010-09-24 22:01
comments: true
categories: OSX ZSH
---
<blockquote>
Terminal is perfect tool for hackers but it can increase anyones productivity.
</blockquote>

ZSH is my default shell in Mac now. Read [My Extravagant Zsh Prompt](http://stevelosh.com/blog/2010/02/my-extravagant-zsh-prompt/ or "zsh: The last shell you'll ever need!":http://friedcpu.wordpress.com/2007/07/24/zsh-the-last-shell-youll-ever-need/) why you should choose ZSH over OSX's default Bash.

### Tip 1: Use key bindings.

Here are some of my favourites:

#### Moving around:

* Ctrl + a _Jump to the beginning of line_
* Ctrl + e _Jump to the end of line_
* Crtl + left/right _Jump word_

Note: you must [configure](http://blog.labrat.info/20100408/ctrl-left-arrow-on-osx/)
Terminal key bindings to make Crtl+arrows work for OSX.

####Clearing:

* Ctrl + L _Clear the screen_
* Ctrl + U _Clears the line before the cursor_
* Ctrl + K _Clear the line after the cursor_
* Ctrl + W _Delete the word before the cursor_


####Searching & History:

* Ctrl + r _Search history_
* Ctrl + j _Stop search and edit_

###Tip 2: use preconfigured environments

Use shell framework such as [oh-my-zsh](http://github.com/robbyrussell/oh-my-zsh)
or [dotfiles](http://github.com/ryanb/dotfiles).
They give you helpers and preconfigured, easily customized environment which
can be shared easily with all your computers. I chose oh-my-zsh and my
configuration is available on
[GitHub](http://github.com/mikkolehtinen/oh-my-zsh).

###Tip 3: replace Terminal.app

Replace Terminal.app with improved terminals with added features.
Some options are [iTerm](http://iterm.sourceforge.net/) or [Visor](http://visor.binaryage.com/), which I'm using.

*Update 05/2012:*
I'm using [iTerm2](http://www.iterm2.com/) now.