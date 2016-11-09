---
layout: article
title: "Build Spine applications with Brunch"
date: 2013-12-03
permalink: "/blog/2013/12/04/build-spine-applications-with-brunch/"
---
Crafting JavaScript web applications can be a tedious process without a scalable structure. The rise of MVC (or MV*) frameworks for JS, had a huge impact on the web and for sure, it defined new standards. Standards that we developers, can't ignore anymore.

However, choosing the right JavaScript MV* framework can be tricky and it really depends on your needs. There are tons of available options out there like, [Backbone](http://backbonejs.org/‎), [Spine](http://spinejs.com/), [Knockout](http://knockoutjs.com/), [Ember](http://emberjs.com/) and more. My choice was, well, Spine.

Spine is a lightweight framework authored by [Alex MacCaw](http://twitter.com/maccaw) and it's entirely written in CoffeeScript. When I first read about Spine. I already knew about CoffeeScript but I didn't really have the chance to extensively use it. Though I knew I could use Spine with plain JS, I considered this as great opportunity to switch.

The companion of Spine is [Hem](http://github.com/spine/hem/), which is defined as a "bundler for Node/CommonJS/Web Apps". It essentially provides a seamless environment for Spine based projects. It includes features like dependency management, single .js file build and [Stylus](http://learnboost.github.io/stylus/) compilation to CSS. 

The only drawback I encountered though, was the ability to use SASS instead. So I had to additionally execute a `sass --watch` process in another terminal tab. This wasn't really an issue for me at first, but it started to be annoying later. I needed a way to ditch the hack and that costed me more time than I expected. I had a brief conversation with [Jonnie Hallman](http://destroytoday) who quickly suggested to use [Grunt](http://gruntjs.com). Though he didn't have the chance to try it before, I decided to give it a spin. Basically, Grunt is a JavaScript task runner, so I had to write all the tasks that Hem gives for granted, by hand. I ended up with a huge [Gruntfile](http://gruntjs.com/sample-gruntfile) which soon became another issue rather than a fix.

Luckily I came across [Brunch](http://brunch.io/), by [Paul Miller](http://paulmillr.com/)
which is a build tool for HTML5, with very little configuration. It already provided a boilerplate for Spine, but as I gave a look at it, I decided to build my own skeleton.

Why? Hem, uses CommonJS to wrap JavaScript or CoffeeScript modules into a single, minified .js file. The great thing about Hem is that it even modularizes the Spine framework components. I could then use:


```coffeescript
Spine = require("spine") # <- this is the real deal!

class App extends Spine.Controller
  constructor: ->
    super
```

The fact that I can "import" my class dependencies first, by using `require('spine')`, makes sense to me as I come from programming languages that used to `import` classes. [In the available Brunch Spine skeleton](https://github.com/damiencaselli/spine-on-brunch), Spine is included in the HTML file as a vendor.js file which is minified but not wrapped into a CommonJS module. This means I don't have to require Spine on every CoffeeScript class because that would be already "loaded". Which for me, at least, didn't feel right. If I'm using a particular class, I should import it. It's an extra line of code, yes, but feels better and makes the whole thing more readable as well.

That's why I wrote my own [Brunch with Spine Skeleton](https://github.com/omarfouad/brunch-spine-sass). It wraps jQuery, Spine, my classes (and whatever else you want) into CommonJS modules and it joins them in one `application.js` file. It also (at last!) compiles `SASS` files into one `style.css` minified file for me. The great thing about it, is that thanks to Brunch, you are up and running right away and optional configuration is a breeze.

[Give it a try](https://github.com/omarfouad/brunch-spine-sass), it comes with a [comprehensive guide](https://github.com/omarfouad/brunch-spine-sass/blob/master/README.md) to get you started in no time. Feel free to [@omarfouad](http://twitter.com/omarfouad) me if you have questions.