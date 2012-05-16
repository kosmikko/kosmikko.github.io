---
layout: post
title: "Programmer's keyboard layout"
date: 2010-10-15 22:16
comments: true
categories: osx productivity
---
> True hacker makes his own keyboard!

Well, maybe not. But many international keyboard layouts suck for
programming. For example with Finnish keyboard you have to type *Alt +
Shift + 8* to get *}*. Now that’s a pain to type gazillion times a day.

To increase my productivity, I decided to create my own keyboard layout
using [Ukelele][] OSX app. First I was considering using [Dvorak][] or
its [programmer’s variant][], but then I realized it would have its
issues with VIM key mappings and having to relearn everything would
require a lot of practicing. So instead I based mine on the Finnish
layout.

<!-- more -->

### ml-keyboard

![][]

As you can see I’ve replaced all dead keys with something useful, and
also I’m using *&aring;* for *{*, since I hardly ever write Swedish. If
I really need it, I can get it with *Alt+&\#229;*. I’ve also mapped my
*caps lock* to *esc* using [PCKeyboardHack][], which makes VIM much more
pleasurable to use.

With shift pressed it’s much like Finnish keyboard without shift with
some additional Django and ZSH helpers.

![][1]

See the result in [GitHub][] or clone the layout <code>git clone
[git@github.com][]:mikkolehtinen/ml-keyboard.git</code> and copy
*ml-keyboard.keylayout* to */Library/Keyboard Layouts*. Then you need to
enable keyboard layout from *System Preferences/Language & Text/Input
Sources*.

  [Ukelele]: http://scripts.sil.org/cms/scripts/page.php?site_id=nrsi&id=Ukelele&_sc=1
  [Dvorak]: http://en.wikipedia.org/wiki/Dvorak_Simplified_Keyboard
  [programmer’s variant]: http://www.kaufmann.no/roland/dvorak
  []: http://i53.tinypic.com/mbpgg7.gif
  [PCKeyboardHack]: http://pqrs.org/macosx/keyremap4macbook/extra.html
  [1]: http://i52.tinypic.com/208fuzb.gif
  [GitHub]: http://github.com/mikkolehtinen/ml-keyboard
  [git@github.com]: mailto:git@github.com

