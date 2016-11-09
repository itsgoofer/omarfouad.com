---
title: My journey from Rails to Sinatra to Node
date: 2015-03-17 00:00:00 Z
permalink: "/blog/2015/03/17/jurney-from-rails-to-sinatra-to-nodejs/"
layout: article
---

Last week I invested some time into re-learning [Node.js](http://nodejs.org). The last time I visited the platform it was young and interesting, but it wasn't mature enough in my opinion. At the time, Ruby was my language of choice for the server side work, along with Rails which boosted our productivity in the past two years.

Some time ago though, I had the itch to explore a lighter server side tech. Rails is good but its structure is too strict. When you create a project in Rails, it gives you a directory structure with tons of files and folders which _magically_ work together. That's good in a way, because enforcing a particular workflow makes us write better code, but at the end it killed the programming creativity that all we developers dream about. 

It all started a few months ago, when I began working on our new project [Finish.io](https://finish.io). I've been using [Angular](https://angularjs.org/) in a Node environment, so I needed [Browserify](http://browserify.org/) for bundling and [Gulp](http://gulpjs.com) to wire everything together along with some tasks like compiling sass, hinting JS and so on. Every single controller, directive or service is attached on `module.exports`, to be then `required` in another file. This workflow is brilliant and I got used to it. I was basically using Node in an _indirect_ way and it made a lot of sense. I say indirect because some people think Node is just for serving files or handling requests on a server. Node is actually more than that.

The best thing was that I had full control over the structure of this project. It started with a `package.json` file, then a `gulpfile.js`. Eventually I added more folders and managed to improve this workflow. The best part? I knew how it worked. In Rails my mind always asks questions like, 'why is this file here' or 'how does this get included there'. In such situations I would either dive to find the answer, or just move on because of deadlines. 

I knew it was time for me to look for a more lightweight framework like [Sinatra](http://www.sinatrarb.com), and I did. With some researches I put together a neat structure, but I missed the `require` and `module.exports` kind of thing I was used to have in Node. In Ruby you create a module then 1. you `require` it, 2. `include` it or `extend` your current class with it and 3. you call its methods. Those are at least three lines of code. I'm not lazy, but the more I did that the more I missed Node and JavaScript. In Node you `require` a module and boom. You can even do stuff like:

```js
var module = require('myModule').callThis();
```

Of course this is a personal preference. There are a lot of Ruby lovers who would attack me because of the last paragraph and I can't blame them. At the end, every dev has to be comfortable with their tools and I found my sweet spot. 

The biggest advantage for me is that it's all JavaScript. I've been eating bread and JavaScript for the last couple of years now, so the ins and outs of the platform were easy to grasp. I then re-wrote this entire blog using Node, because the best way to understand a new framework/platform/library is to actually use them in a real life project. This site is now running on our Ubuntu server, served by [Express](http://expressjs.com/), connected to a Postgres database using [Sequelize](http://docs.sequelizejs.com/en/latest/) and rendering views with [Handlebars](http://handlebarsjs.com/)–all this proxied by Nginx. I even wrote a small CMS with Angular to manage posts and pages. And because of JavaScript, I wrote all this without even blinking an eye. 

Previously, this blog was written using [Liquid](https://github.com/Shopify/liquid) templates and generated using a blog engine written in Ruby called [Jekyll](http://jekyllrb.com/). The engine would take your templates and posts and spit a static  files based website ready to be upload and served statically. 

Though I didn't mind this workflow, the need to learn a new server side platform pushed me to make the switch. And I'm glad I did. 
