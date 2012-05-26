---
layout: post
title: "Rewindy tech stack"
date: 2012-05-25 21:16
comments: true
categories: Backbone.js Python HTML5 JavaScript CoffeeScript
---
I haven't seen too many [good](http://blog.fogcreek.com/the-trello-tech-stack/) blog posts on how to build architecture for a modern, frontend-heavy HTML5 app, so I thought I'd try to explain our stack while choices are still fresh in memory. We are just [getting started](http://www.rewindy.com) (just started private alpha) so things will still change and evolve.

Don't reinvent the wheel. I believe that especially in an early-stage startup the best code is the code you don't need to write yourself(SaaS) and best way to host your server is let someone do that for you (PaaS). When choosing components for [Rewindy](http://www.rewindy.com), we opted for choices that got us moving as fast as possible. Performance and scalability are good to keep on mind but there's time for optimizing later, if the app succeeds.

Plan was to use a third party service or existing component whenever possible instead of building our own, even though sometimes that means making few compromises. That allows us to maximize the development on our core business.  Surely later it makes sense to start replacing suboptimal parts in the system with custom code or servers, if that is needed for cost or performance reasons. Still it's good to keep in mind that often you don't need library or framework for that feature, a few lines of code will do just fine. Choose the best tools for the job.
<!-- more -->

#Backend
I prefer expressive and quick to write languages that allow us to have as little code as possible. In the backend we have pretty much just a rest API, so we could've chosen any language. Python was our choice, since it's a great versatile language for the web and it's one of the most readable languages when used properly.

Really, [it doesn't really matter](http://www.codinghorror.com/blog/2008/05/php-sucks-but-it-doesnt-matter.html) you can write your backend using [Brainfuck](http://en.wikipedia.org/wiki/Brainfuck) or [PHP](http://me.veekun.com/blog/2012/04/09/php-a-fractal-of-bad-design/), but I believe it's crucial to enjoy the language and keep the focus in writing the relevant stuff. With Python it's easy to keep your code simple and maintainable.

In Finland most companies, even startups still tend to favor PHP and Java, most likely since there's a lot of cheap developer power available. Still, finding good developers for any language is hard.

##Hosting
There's [many](http://kencochrane.net/blog/2011/06/django-hosting-roundup-who-wins/) options for hosting Python apps. We picked Heroku since it's mature, well [backed](http://blog.heroku.com/archives/2010/12/8/the_next_level/) and offers great set of tools for developers. Sure there's [some things](http://tech.blog.aknin.name/2012/03/09/heroku-is-great-however/) that could be better, but overall it's a great choice.

We are storing our files in Amazon S3, we also use S3 for serving our static assets (except for fonts, which we use Rackspace Cloud Files due to [this issue](https://forums.aws.amazon.com/thread.jspa?threadID=34281)). We picked S3 mainly due to wide integration options and e.g. better CLI tools than Cloud Files. Hosting static assets outside Heroku is highly recommended and this setup has been working great.

##MongoDB
We decided to use document datastore mainly to get moving fast by moving complexity from application level to database. MongoDB is good general use document store, with great developer experience. MongoLab is currently our SaaS service, no issues so far but let's see how it works when we get more traffic. I'm aware of the scaling issues there might be, but 10gen has been improving e.g. [locking issues](http://blog.serverdensity.com/2012/05/23/goodbye-global-lock-mongodb-2-0-vs-2-2/) and things are getting better. Be sure to check the [critisism](http://blog.engineering.kiip.me/post/20988881092/a-year-with-mongodb) and evaluate carefully whether Mongo is the best choice for your data and app.

####[MongoEngine](http://mongoengine.org/)
MongoEngine is a Document-Object Mapper (like ORM, but for document databases) for working with MongoDB from Python. It's one of the most mature ones available for Python. It's actively developed and they just came out with 0.6.x with bunch of [nice features](https://github.com/MongoEngine/mongoengine/blob/master/docs/changelog.rst). Requires [PyMongo](http://api.mongodb.org/python/current/), which is just about to get [Gevent](http://api.mongodb.org/python/current/examples/gevent.html) support. If you're looking for async support you might want to watch [this webinar](http://www.10gen.com/presentations/webinar/Asynchronous-MongoDB-with-Python-and-Tornado).


##[Flask](http://flask.pocoo.org/)
This time, Django had to go. I wanted fast, quick to tweak framework, with just the minimal functionality and not a massive beast with heavy footprint and too much magic. Flask has a good selection of extensions to plug in the missing features and seems to be the most popular Python micro framework. Flask has been a pleasure, haven't a missed a thing yet from Django. Flask has great documentation (not as good as Django though), it's fast, clean and you can even read the full code without pulling your hair out.

These are the most relevant extensions we use:
####[Flask-wtf](http://packages.python.org/Flask-WTF/)
Offers helpers for [WTForms](http://wtforms.simplecodes.com/docs/dev/) integration, CSRF protection, etc. We use WTForms for admin forms and validating AJAX posts.
####[Flask-mongoengine](https://github.com/sbook/flask-mongoengine)
Provides integration to Flask and WTForms.
####[Flask-Babel](http://packages.python.org/Flask-Babel/)
Adds i18n and l10n support to Flask.
####[Flask-Script](http://packages.python.org/Flask-Script/)
Provides support for writing external scripts in Flask. Think manage.py in Django.
####[Flask-Login](http://packages.python.org/Flask-Login/)
User session management. Our login solution is built on top of Flask-Login and [passlib](http://pypi.python.org/pypi/passlib)

## Redis & RQ
[RQ](http://python-rq.org/) is a Redis-based simple library for queuing tasks and processing them on background. It works great for our simple needs, and avoids the complexity of Celery and the likes. [Redis To Go](https://devcenter.heroku.com/articles/redistogo) has a Heroku addon.

#Frontend

Most of our code is on the frontend, so it was very important to pick tools that allow us to scale and maintain quite large and complex JavaScript application.

##[Backbone.js](http://documentcloud.github.com/backbone/)
Backbone.js has a good momentum and quite active community. There's already many common problems solved with Backbone, so you don't have to reinvent the wheel. It's the common ground for many frontend heavy projects. There's stellar documentation and plenty of examples available.

Yet Backbone is very tiny and light weight, so you have to prepared to write a lot of basic code on top of it or use some of the helper frameworks mentioned below. If you're looking for more of a full stack framework, check [Addy Osmani's TodoMVC comparison of popular MVC frameworks](http://addyosmani.github.com/todomvc/).

There's quite many developers with some level of knowledge for backbone which helps recruitment. Though a good JavaScript developer can pick up any framework and be productive in few days.

###[Chaplin](https://github.com/chaplinjs/chaplin)
There's few good meta frameworks for backbone out there, check also [Thorax](http://walmartlabs.github.com/thorax/),  and [Backbone.Marionette](http://derickbailey.github.com/backbone.marionette/) solving many common issues. We picked Chaplin, since it has good docs, cleanest code and it provided solutions to our needs and was based on same tools that we are using. Read the [introduction](http://9elements.com/io/?p=680) if interested.

Chaplin is the basis of our application, and it was quick to build our custom stuff on top of it. Recently it has been developed more towards real framework, but it's still undergoing pretty heavy refactoring so be prepared for sudden changes.

###[Backbone.modelbinding](https://github.com/derickbailey/backbone.modelbinding)
This plugin provides a simple, convention based mechanism to create bi-directional binding between your HTML form input elements and your Backbone models

###[Backbone.validation](https://github.com/thedersen/backbone.validation)
Reusable validation rules both to validate your model and to validate form input, as well as providing a simple way of notifying users about errors when they are populating forms. [Read more](http://thedersen.com/2011/12/28/backbonevalidation/)

## [CoffeeScript](http://coffeescript.org/)
With CoffeeScript, writing JavaScript becomes fun again. Code is cleaner and more verbose, leading to faster development and less bugs. Sure there's opinions [against it](http://ryanflorence.com/2011/case-against-coffeescript/), but seems that many have [changed their opinion](http://ryanflorence.com/2012/javascript-coffeescript-rewrite/)? Just saying that you should try it. I'm not going back to vanilla JS. Debugging is not an issue when you use require.js. And plz don't make those uncomprehensible magic one-liners.

- [The Little Book on CoffeeScript](http://arcturo.github.com/library/coffeescript/)
- [Smooth CoffeeScript — Interactive Edition](http://autotelicum.github.com/Smooth-CoffeeScript/interactive/interactive-coffeescript.html)
- [Js2coffee: convert JavaScript code to CoffeeScript](http://js2coffee.org/)


##[jQuery](http://jquery.com/)
We are using jQuery for DOM manip, animation, ajax, etc. basics. I would like to replace it with something more lightweight (e.g. Zepto/Ender), but that would need some work since for example Bootstrap depends on jQuery (though there seems to be unofficial [Ender port](https://github.com/rvagg/ender-bootstrap) of Bootstrap).

##[Require.js](http://requirejs.org/)
We use require.js for:

- AMD script async loading and helps to organize code into files
- RequireJS text add-on (to assist with external template management)
- r.js for handling script optimization

Require.js is flexible and versatile way to load files and modules, and I don't see the slightly verbose syntax as an issue. I haven't optimized module loading yet, so we are serving code in a single file. It would make sense to at least separate the code that rarely changes, so users don't have to reload all the code. There are other options for handling file loading and dependency management, check at least [LABjs](http://labjs.com/),[Browserbuild](https://github.com/LearnBoost/browserbuild) and [Brunch](http://brunch.io/), which btw. has integrated with Chaplin recently.

##[Handlebars.js](http://handlebarsjs.com/)
There's no shortage on client-side templating libraries. Handlebars was our choice, since it supports just enough features for our needs. Mustache style is quite readable, and handlebars supports pre-compilation. There's pretty ok documentation and some plugins available. We easily added helpers for e.g. [i18n](http://rockycode.com/blog/i18n-strings-requirejs/) and date formatting with [Moment.js](http://momentjs.com/).

##File upload
Upload is one of the key parts of our system. Implementing a fast and scalable backend for handling concurrent uploads is not trivial, so we opted to start with 3rd party service for that. [Transloadit](http://transloadit.com/) is [based on](http://debuggable.com/posts/parsing-file-uploads-at-500-mb-s-with-node-js:4c03862e-351c-4faa-bb67-4365cbdd56cb) Node.js and formidable, they have great customer support and the pricing is ok for us, at least until our traffic increases significantly.

Key thing for choosing Transloadit was that they allow storing our data in our own S3 buckets, so in case we want to change the service provider or implement our own solution, migration is a non-issue. Integration was a snap, and even though they've had some hiccups in the service, thing seem to be improving all the time. Check also [Blitline](http://www.blitline.com/) for an alternative SaaS solution.

##[Twitter Bootstrap](http://twitter.github.com/bootstrap/)
We decided to start with a full stack css framework, providing basic UI components and responsive grid. Twitter bootstrap provides all that, and more, so there's need to remove some unnecessary parts. The components are good quality and look ok out of the box, not quite as nice as [Zurb Foundation](http://foundation.zurb.com/), but JavaScript components are a bit more extensible better suited for dynamic apps. Cons for Bootstrap is its large size, dependency on jQuery, and it's quite hard to choose subset of css components. I'd rather start from bare bones and add components as needed.

##[Stylus](http://learnboost.github.com/stylus/)
I highly recommend using a CSS preprocessor for writing cleaner and more maintainable CSS. Still not sure if Stylus is currently the best choice. We considered SASS too and I have more experience with that. Stylus is perhaps slightly less verbose and open for different syntaxes. SASS on the other hand offers nice features such as media query bubbling, which would be great for responsive design. Check [this post](http://retype.posterous.com/switching-from-sass-to-stylus) for more comprehensive review.

## Code structure
I'm really not a fan of Rails convention of organizing files and folders. So instead of stashing all my views under a single 'views'-folder, I put my files under a subfolder for module or functionality. See below for example. This really helps you finding your files and keeping your sanity when you have tens or hundreds of files. I keep my files short and simple, so just one thing per file.

```
├── Makefile
├── README.md
├── app
│   ├── application.coffee                        Application bootstrapping
│   ├── assets
│   │   └── images
│   │   └── css
│   ├── chaplin                                   Chaplin .coffee files
│   ├── initialize.coffee                         Require.js config
│   ├── login                                     This is an example application module in our app
│   │   ├── controllers
│   │   │   └── login_controller.coffee
│   │   ├── locale
│   │   │   └── en-GB.coffee
│   │   ├── models
│   │   │   └── credentials.coffee
│   │   ├── templates
│   │   │   ├── login_form.hbs
│   │   │   └── login_modal.hbs
│   │   └── views
│   │       ├── login_form.coffee
│   │       └── login_modal.coffee
├── app.build.js                                  Require.js build file
├── build                                         (Automatically) Compiled JS & CSS files
├── prod-build                                    Production(minified) build
├── test                                          Tests .coffee files
└── vendor
    ├── bootstrap                                 Twitter Bootstrap
    │   ├── img
    │   ├── js
    │   └── less
    └── js                                        Misc. 3rd part JS files
```

## Build process
There's two needs in our case, the build tool needs to compile _.coffee_ and _.styl_ files on the fly and concatenate and minify files for production. There [lots of tools](http://blog.paracode.com/2012/02/15/build-mgmt-javascript-coffeescript/) for that, but we couldn't find any tool perfectly fitting our needs, so I wrote our custom script using Gnu Makefile. Little bit based on TJ Holowaychuck's [Makefiles](https://github.com/visionmedia/uikit/blob/master/Makefile) and thx to Vli for the base version.

That's very lo-fi but gets the job done quickly and it's easily extensible and modifiable. I'll try and share our Makefile later. Check also [Grunt](https://github.com/cowboy/grunt), which seems good and has nice plugins for many needs.


# Testing
##Jenkins
De-facto standard for continuous integration servers. Travis looks good but it's not yet available for private repos. Jenkins runs our test suite and continuosly deploys to our staging server in Heroku. Production deployments are currently triggered manually using our deploy script.

##[Mocha](http://visionmedia.github.com/mocha/)
Mocha is the basis of our front end unit testing framework. It's customisable enough to support both behaviour-driven and test-driven development styles, it has lots of test reporter format options which makes it easy to fit our CI and workflow. Only issue I've had with it, is how to integrate it with require.js when running tests from both Node.js & browser? Check this [tutorial](http://blog.james-carr.org/2012/01/16/blog-rolling-with-mongodb-node-js-and-coffeescript/) on how Mocha works. Our test tools also include [expect.js](https://github.com/LearnBoost/expect.js) and [Sinon.js](http://sinonjs.org/)

##[Zombie.js](http://zombie.labnotes.org/)
We haven't had too much time to write acceptance/full-stack tests yet, but so far Zombie.js seems ok for the job. Other options could be Phantom.js with Casper.js or some SaaS solution like SauceLabs.

##Backend unit tests
We are using basic [unittest](http://docs.python.org/library/unittest.html) + [Flask-testing](http://packages.python.org/Flask-Testing/) extension and running our tests with [nose](https://github.com/nose-devs/nose).

## Updates
Join in the discussion on [Hacker News](http://news.ycombinator.com/item?id=4024923)