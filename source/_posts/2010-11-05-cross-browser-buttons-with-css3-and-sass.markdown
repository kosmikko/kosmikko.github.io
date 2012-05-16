---
layout: post
title: "Cross-browser buttons with CSS3 and Sass"
date: 2010-11-05 22:32
comments: true
categories: css3 gae sass
---
Using CSS3 isn’t quite as easy as it should be. Lots of advanced
functionality requires using vendor prefixes which easily bloats your
stylesheet with repetitive properties.

One solution to this is using [Sass][]. Especially Sass mixins and
[color functions][] can radically reduce the amount of code you need to
write. Combine that with [CSS3Pie][] to add IE support and you have
quite elegant solution for making cross-browser supported CSS.

<!-- more -->

### Making buttons

Here’s one way of making nice looking buttons with pure CSS3. Rounded
borders and gradient can be added this way with no extra markup or
images.

#### Enable CSS3Pie support

{% codeblock PIE usage lang:sass %}
@mixin pie
  //using CSS3Pie for IE support
  behavior: url(/PIE.htc)
.pie
  @include pie
{% endcodeblock %}

On a side note, if you want to serve PIE.htc with app engine add this to
your app.yaml:
{% codeblock %}
- url: /PIE.htc
  static_files: media/css/PIE.htc
  mime_type: text/x-component
  upload: media/css/PIE.htc
{% endcodeblock %}

#### Sub mixin for gradients

{% codeblock Vertical gradient lang:sass%}
@mixin vertical-gradient($bgcolor1, $bgcolor2, $bgcolor3, $bgcolor4)
  // cross browser mixin for gradients
  @extend .pie
  background: $bgcolor1
  background: -moz-linear-gradient(0% 100% 90deg, $bgcolor1 0%, $bgcolor2 50%, $bgcolor3 50%, $bgcolor4 100%)
  background: -webkit-gradient(linear, 0 0, 0 100%, color-stop(0, $bgcolor4), color-stop(0.5, $bgcolor3), color-stop(0 ...
{% endcodeblock %}

With this mixin you can give four colors as parameter which are included
in gradient from bottom to top.

#### Mixin for rounded corners

{% codeblock Rounded borders lang:sass %}
@mixin round-borders($bordercolor, $tl: 8px, $tr: nil, $br: nil, $bl: nil)
  // cross browser mixin for rounded borders
  @extend .pie
  border: 1px solid $bordercolor
  @if $tr == nil
    $tr: $tl
  @if $br == nil
    $br: $tl
  @if $bl == nil
    $bl: $tl
  -moz-border-radius: $tl $tr $br $bl
  -webkit-border-top-left-radius: $tl
  -webkit-border-bottom-left-radius: $bl
  -webkit-border-top-right-radius: $tr
  -webkit-border-bottom-right-radius: $br
  border-radius: $tl $tr $br $bl
{% endcodeblock %}

Parameters include border color and radius of roundness for each corner. For example `include
round-borders(\#fff) would make 8px round white borders.

#### Button mixin

See the complete code:

<script src="https://gist.github.com/664623.js?file=buttons.sass">
</script>
#### Usage

Put above code in \_buttons.sass and include it in your style sheet. For
example:

{% codeblock Example usage lang:sass %}
@import "_buttons.sass"
$color1: #155163
...
button.blue
  @include mybutton($color1, $color2, $color3, $color4, white, $color1, $color1)
{% endcodeblock %}

That should work after you define the colors.

  [Sass]: http://sass-lang.com/
  [color functions]: http://nex-3.com/posts/89-powerful-color-manipulation-with-sass
  [CSS3Pie]: http://css3pie.com/

