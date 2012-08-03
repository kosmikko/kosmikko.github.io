---
layout: post
title: "Structuring complex Backbone.js apps"
date: 2012-08-03 16:45
comments: true
categories: Backbone.js JavaScript Architecture
---
Building a more complex  JavaScript app can easily get out of hand if no effort is put into architecturing the application.  In this post I'm overviewing some of the most common higher level architectural patterns and concerns that you should consider when building modular single page apps using Backbone.js.

Transition from building traditional web apps doing full page reloads into dynamic single page app often requires rethinking the application architecture. You cannot just hack together some random jQuery code, because you're storing application state in client and managing it quickly becomes spaghetti. On the other end I've seen some bad examples of [over-engineering](http://www.johndcook.com/blog/2009/07/27/baklav-code/) your app, so think carefully what kind of architecture suits best for your app. 

Single page apps should usually be architectured more like desktop apps, thus Smalltalk-80 like MVC fits quite naturally as a basis for modular, object-oriented application's architecture. MVC is only part of large application's architecture though - it only solves how to layer your modules into models, views and controllers.

Backbone.js provides an easy starting point for MV* like structure, but it offers just mainly low-level patterns. What about bindings between objects, inter-module communication, dependency loading, handling  JST templates, view layouts, memory management and object disposal? The former are example issues that Backbone.js leaves open for the developer to implement. This has been a design choice with Backbone.js - it has small core but missing parts can be added as libraries when needed. 

Now in the case of more complex application you could pick some of the more full stack frameworks, add some of the open source projects providing the missing parts on top of Backbone.js or build your own framework. There's always a tradeoff with large frameworks, e.g. ember.js provides much more out of the box but it's 20K LOC and opinionated. 

I prefer a micro-framework approach where you have a small core framework and you can easily add additional libraries. Bindings, dependency loading, etc. higher level architectural issues are things that many developers disagree on how to implement those. Backbone.js leaves it open for developers mix and match components that suit their needs best, which is great.

## Communication between objects and modules
One key method for keeping application code maintainable is avoiding strong coupling between application modules. Loose coupling is good because it helps you to break your code into smaller and more maintainale blocks. This is much better option than strong coupling i.e. objects calling directly methods of other objects, which will slow down development and make your application difficult to maintain.

One commonly used pattern to ensure loose coupling is the Observer pattern or Publish/Subscribe (Pub/Sub) pattern. This pattern uses an message(event) channel between the objects receiving notifications (subscribers) and the object firing the event (the publisher).  The publisher can then use event channel to inform all subscribers that something has happened.  This is very useful when building UI components, for example you can fire an event when a button was clicked and observers which could be other UI components can react to that event independently. 

You can trigger events using [Backbone.Events'](http://backbonejs.org/#Events) *trigger*, subscribe to events with *on* and unsubscribe with *off*. But to achieve decoupling you need a mediator(middleman) between the publisher and subscribers, so that the objects don't need to know the details (such as lifetime) of others. Examples on how to implement mediator pattern see Addy Osmani's [patterns article](http://addyosmani.com/largescalejavascript/#mediatorpattern) or Chaplin's [mediator](https://github.com/chaplinjs/chaplin/blob/master/src/chaplin/mediator.coffee). 

I like to use the following naming conventions for subscribing an event:

{% codeblock lang:javascript %}
mediator.subscribe('module:action', this.functionToCall);
{% endcodeblock %}

Thus subscriber's function *functionToCall* gets called when *'module:action'* event is triggered by a publisher. The publisher can publish the event with:

{% codeblock lang:javascript %}
mediator.publish('module:action', params);
{% endcodeblock %}

Here *'module:action'* is just a convention for the event channel. For example *'login:dialogOpen'* event could be fired when user opened the login dialog. This prefixing the action id with module name makes it a bit easier to track which module is triggering the event. For app wide global events I tend to use a prefix such as *'global:globalEvent'*.

##Bindings and validation
Object bindings are needed to keep properties between two different objects in sync, and making sure changes get propagated in either direction. Common use case for this pattern is binding your Model attributes to View elements. For example you want to change a form input when Model gets changed and update model based on user input. 

Now this can be done with just *Backbone.Events*, but manually binding and unbinding events and re-rendering the view gets inefficient and causes bugs.  You will most likely want to use some of the open source libraries for handling automatic bindings. Check for example [Backbone.ModelBinder](https://github.com/theironcook/Backbone.ModelBinder).

Another common need is validating Models based on form input and providing user feedback based on that. Backbone has a method for [Model validation](http://backbonejs.org/#Model-validate), but it is left blank and needs your custom validation logic. It makes sense to create reusable validation logic here. One good plugin for handling validation easily is [backbone.validation](https://github.com/thedersen/backbone.validation).

##Template handling
Use the templating engine of your choice (I'm using [Handlebars.js](http://handlebarsjs.com/)), if you don't like the syntax or features of the default Underscore templates.

If performance is a concern, make sure that your template engine supports precompilation. That means that template is precompiled into JavaScript code, so that the client doesn't have to compile the template on the fly and you don't need to include the whole template engine code in your app. 

You can do this precompilation step server side or when building the JavaScript files for production. See [require-handlebars-plugin](https://github.com/SlexAxton/require-handlebars-plugin) for an example with Require.js and Handlebars. This plugin also provides other handy helpers such as automation for registering partials and helpers.

Most server side precompilers e.g [Jammit](http://documentcloud.github.com/jammit/) or [Rails Asset Pipeline](http://nicolai86.eu/posts/2011-09-21/rails-3-asset-pipeline-javascript-templates/) will add compiled templates into top-level window.JST object.

##View managment
Backbone Views are very lightweight, so in almost any app you will need some helpers for view rendering, event binding, and lifecycle management. Usually it makes sense to subclass *Backbone.View* in your application's base View, and add the helpers there to be available for all Views.

View's rendering helper takes care of mapping  JavaScript objects into DOM elements, otherwise writing this boilerplate manually per View gets tedious. Also rendering template into correct place in the UI should be abstracted. Most complex apps will need multiple screens with multiple subviews and manually managing subviews can lead to unmanageable code. 

Lifecycle methods are needed for reacting into lifecycle events when needed, e.g. doing stuff after View is rendered or disposed. For example if you want to modify View's DOM, you can only do that after View is rendered.

See [Marionette's View](https://github.com/derickbailey/backbone.marionette/blob/master/docs/marionette.view.md)
or [Chaplin's View](https://github.com/chaplinjs/chaplin/blob/master/src/chaplin/views/view.coffee) and if you're not using either pick the parts you need. Check also Rebecca Murphey's [deck](https://speakerdeck.com/u/rmurphey/p/new-dogs-old-tricks-or-dojo-already-did-that) on this topic.

##Memory management
One major source for bugs in single page apps is memory leaks. Since you're not doing full page reloads to flush the memory, you'll need to be careful in correctly handling object disposal. Avoiding global variables is the basic thing to do, but you can still clutter memory with zombies if you don't clean up references after your objects correctly. Backbone.js does not  clean up objects for you, you need to make sure to de-reference your objects to let  JavaScript runtime's garbage collector do its job.

Especially make sure to unbind from *Backbone.Events* when object is disposed. Common scenario is when switching Views in a region in your app, Backbone handles replacing the DOM with new view's content. But unless you correctly unbind the previous View from all events, it's left hanging around in memory still subscribed to the events.
 
Good way to clean up memory after an object, is to implement some kind of automation for memory management in the lifecycle methods. For example setup conventions for calling a cleanup function before de-referencing the object. Chaplin objects have the [dispose method](https://github.com/chaplinjs/chaplin#memory-management-and-object-disposal), which in case of Views takes care of unbinding from all events and cleaning up View's subviews. Marionette.View has similar [close method](http://derickbailey.github.com/backbone.marionette/docs/backbone.marionette.html#section-19).

##Conclusions
Designing the perfect architecture for single page apps is not trivial. Think about the design goals for the architecture - you'll want the code to be clean, fast and maintainable. 

Experience is the best way of learning on how to architecture apps, but you can also learn a lot from others. Backbone.js is probably not the easiest choice for beginners, since it leaves a lot of choices open to the developer. If you have no previous experiene in large single page apps, you should carefully study the architecture of the the more full stack MVC frameworks.

If you decide to go with Backbone, see the design choices made in these Backbone based application frameworks:

- [Chaplin](https://github.com/chaplinjs/chaplin)
- [Thorax](http://walmartlabs.github.com/thorax/)
- [Backbone.Marionette](https://github.com/derickbailey/backbone.marionette)

If you're not familiar with software design patterns I recommend reading still relevant [Gang of Four's "Design Patterns"](http://en.wikipedia.org/wiki/Design_Patterns) or ["Patterns of Enterprise Application Architecture" by Martin Fowler](http://www.amazon.com/Patterns-Enterprise-Application-Architecture-Martin/dp/0321127420). Also Addy Osmani has written quite many [detailed](http://addyosmani.com/largescalejavascript/) [articles](http://addyosmani.com/resources/essentialjsdesignpatterns/book/) on patterns for large JavaScript applications.