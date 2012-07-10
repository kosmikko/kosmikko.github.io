---
layout: post
title: "Using GNU Make as a frontend build tool"
date: 2012-07-10 18:24
comments: true
categories: 
---
There seems to be hundreds of language specific build tools for everyone's needs, and somehow I feel that wasted energy could be spent on building more useful things. Often a small [Makefile](http://en.wikipedia.org/wiki/Make_(software\)) is all that you need. 'make' may seem like complicated tool at first but learning the basics is quick and highly recommended.

[GNU make](http://www.gnu.org/software/make/manual/make.html) has been built for detecting which parts of a program need to be recompiled, and issuing commands to recompile them. The most common use cases for frontend asset building are:

*  You need to concatenate and minify your assets for production use
*  You want to compile *SASS/LESS/Stylus* files to *CSS* and *.coffee* files to *.js* when developing. To make things convenient this should be automatically done on file modification without the need to run any commands.
*  You want to run other tasks on file save; such as linting or running tests
*  Deploy files to production
<!-- more -->
Using 'make' is convenient because you don't need to introduce another dependency for your project since every developer should have 'make' preinstalled (as long as they are using Unix based OS). Once you learn it, it's easily portable, and can be used any project regardless of the environment, avoiding the need to learn language specific build tools. If you build your Makefiles right, it should be just few lines of code, which makes it simple and easy to maintain.

One thing you will need, is a tool for periodically running 'make' . I recommend installing [watch](https://github.com/visionmedia/watch) by [TJ Holowaychuck](https://github.com/visionmedia), which excutes a given command on selected interval. This is very lightweight way of detecting file changes, and doesn't use any processing power when there's no changes to be compiled (unlike when using many language specific build tools).

	git clone https://github.com/visionmedia/watch.git
	cd watch
	make install

Here is a sample Makefile from my [Rewindy](http://www.rewindy.com) project:
<script src="https://gist.github.com/3083602.js"> </script>

When you start working on the code, run
  
		make watch
  
which runs the necessary commands to compile your asset files immediately after change. Combine this with tools like [LiveReload](http://livereload.com/), and you'll have a pretty smooth build process.