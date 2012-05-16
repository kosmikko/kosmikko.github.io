---
layout: post
title: "Decorator for executing a function in a given namespace"
date: 2010-09-24 21:58
comments: true
categories: gae multitenancy
---
The 1.3.6 update for App Engine introduced support for <a href="http://code.google.com/appengine/docs/python/multitenancy/multitenancy.html">multitenancy</a> using namespaces. You might want to read the <a href="http://blog.notdot.net/2010/08/New-in-1-3-6-Namespaces">introduction</a> by Nick Johnson to read what it means. Here is a snippet for function decorator allowing you to run function in given namespace. Code is based on <a href="http://www.reddit.com/r/AppEngine/comments/d2xqv/hey_heres_a_little_one_for_python_run_in/">this</a>.
<!-- more -->

<script src="http://gist.github.com/593224.js"> </script>