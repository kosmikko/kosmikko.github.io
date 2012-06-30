---
layout: post
title: "Extending Backbone views using mixins"
date: 2012-06-30 17:29
comments: true
categories: 
---
Sometimes you'd like to have shared methods across classes, but it's not possible or it doesn't seem natural to create a shared parent class. CoffeeScript and Underscore make it fairly painless to create reusable patterns for your classes using [mixins](http://en.wikipedia.org/wiki/Mixin).   

When using [Chaplin](https://github.com/chaplinjs/chaplin), application views are normally extending CollectionViews and Views. It usually makes sense to make custom subclasses for both, which application views then can extend. Now, lets say I'd like to add Google Analytics tracking functionality for user actions. I'd like add this tracking functionality to both types of views, thus I cannot create a shared parent class for both. 

One solution could be to make custom base classes for both types, but that would mean that I would need to copy-paste the same method to both base classes, which would make it ugly and difficult to maintain. In this case we need multiple inheritance, which can be implemented using mixins.
<!-- more -->
Our mixin is defined as object literal which has shared attributes and functions required in our views.

<script src="https://gist.github.com/3023870.js"> </script>

Then in your base view you can use this mixin with:

<script src="https://gist.github.com/3023904.js"> </script>

You can also use Mediator's publish/subscribe pattern for doing this elegantly, but this could be one alternative way to do it. In my base_extensions.coffee, there's also view helpers e.g. for i18n and template handling.

Mixins are great way to structure your app into smaller and reusable components. Check also Chaplin's source on how they are using mixin to source in Subscriber which offers functionality to subscribe to global Publish/Subscribe events to any object.

