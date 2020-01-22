---
title: Advanced AngularJS structure with Gulp, Node and Browserify
date: 2015-03-21 00:00:00 Z
permalink: "/blog/2015/03/21/advanced-angularjs-structure-with-gulp-node-and-browserify/"
color: "#7ec0ff"
layout: article
---

Many tutorials about [AngularJS](https://angularjs.org/) show how to create a simple app with a very basic project structure. Today I'm going to show you how to set up a more complex and scalable development workflow which I use in most of my Angular projects, using [Node.js](https://nodejs.org/), [Gulp](http://gulpjs.com/) and [Browserify](http://browserify.org/).

Note that this tutorial assumes you have a good understanding of AngularJS and some basic knowledge of Node. If you are not familiar with these, I strongly recommend you to check them out before reading on.

### Why Node.js

When working on a JavaScript based web application, chances are you end up with a pile of JavaScript files that would end up in your HTML file like so: 

```html
<script src="/js/lodash.js"></script>
<script src="/js/jquery.min.js"></script>
<script src="/js/dependency2.js"></script>
<script src="/js/dependency1.js"></script>
<script src="/js/main.js"></script>
...
```

As you see in the example above, there are a bunch of dependencies like lodash, jQuery and others that you might have written by yourself. Eventually a `main.js` file will be added at the very bottom which is supposed to implement the functionality of those dependencies. If your application is complex the list will become longer. That's not very bad, except the browser will make tons of requests to fetch these files and that your project will be harder to maintain.

In this case, Node comes in handy because it offers a modular system out of the box, providing functions like `require()` and objects like `exports` or `module.exports`.

```js
// hello.js

var $ = require('jquery')
function greet() {
	$('h1').html('Hello World')
}
module.exports = greet
```

```js
// main.js

var hello = require('./hello')
hello() // <h1>Hello World</h1>

```

That's cool, this way we can just include `main.js` to our HTML file which will require jQuery. However this will not work per se. That's because the browser doesn't understand Node modules therefore it doesn't know how to _require_ other JS files. Luckily there is a very useful tool called _Browserify_ which solves this problem. 

### Browserify

Browserify is a Node module that takes your main JavaScript file, read all its _required_ dependencies (and dependencies of dependencies), and spits out a single JavaScript file, ready to be included in your HTML. This file contains JavaScript code that is actually compatible with browsers, in other words, it _browserfies_ your Node modules. You might think that using a plugin to concatenate JS files together would do the same job, but it won't. That's because when concatenating source files you will have to take into consideration their order based on the logic behind each file. The order in which you concatenate your source files is crucial because it potentially affects their execution. What if, for instance, a file is needed after _and_ before another file? Browserify will take care of that.

### Gulp

Gulp is a versatile tool that automates your workflows. It can do things like compiling your `sass` files, optimizing assets, compile CoffeeScript and _browserifying_ your Node modules. There is a gulp plugin for virtually every task you'd need to automate in your development workflow. It also has nifty options like executing specific tasks when a file is saved in your project. For example, you can set it to watch a `.sass` file, so that whenever you save it, Gulp executes a task that compiles that sass file and saves it to your `/public/css` directory.

### Let's start

Now that we have an idea about the tools we're going to use, let's create an Angular based app from scratch. 

### Install Node

You can download Node from [here](https://nodejs.org). Once installed, open Terminal and type:

```shell
node -v
```

This shows what Node version you have on your system.

### Create a project

Now we will make an empty directory and create a `package.json` file. I will assume this directory is on the Desktop:

```shell
cd ~/Desktop
mkdir angular_browserify_gulp
cd angular_browserify_gulp
npm init
```

If you are familiar with Node, `npm init` will fire a command line utility that will ask some information about your new app, like its name, version, license type, etc. Once done, it creates a `package.json` file in the root of the project directory, with all the details included. This file will also include a list of all the dependencies you will use in this project—either `devDependencies`, which are the ones your project will need for the development process, or/and `dependencies`, which are the ones needed by the the app to run, like `jQuery` or `lodash` for example.

For now, let's leave the package file as is, then

### Install Gulp

Open Terminal and install the Gulp package

```shell
npm install gulp --save-dev
```

The above command will tell `npm`, or the Node Package Manager, to install Gulp _and_ add it as a dependency in your `package.json` under the `devDependencies` list, hence the `--save-dev` option. Note that Gulp is considered as a development related dependency, which means it will only run while developing the app.

After you install Gulp, you will find a new directory in your project called `node_modules`. This holds the source code of your installed packages. Of course, if you're going to version control your app, you should add this folder to `.gitignore`, because you don't want to push it to your repo (as this folder will be very big in file size). Then, whoever will clone your app can just run `npm install`, which will install every dependency in your `package.json` file and create a brand new `node_packages` folder for that specific working directory.

### Install some Gulp plugins

Now let's go ahead and install some Gulp plugins. We will start with `gulp-connect` which simply fires a static, localhost server that serves your app statically, since this is a front-end project anyways.

```shell
npm install gulp-connect --save-dev
```

Than let's install a package that compiles `.sass` files into CSS:

```shell
npm install gulp-ruby-sass --save-dev
```

Note that for this one to work you should have the Ruby Sass gem installed on your system. More about it [here](http://sass-lang.com/install).

### Create your first Gulp task

Now let's create a `gulpfile.js` in the root of our project directory and write the following JS code:

```js
var gulp = require('gulp')
var sass = require('gulp-ruby-sass')
var connect = require('gulp-connect')

gulp.task('connect', function () {
	connect.server({
		root: 'public',
		port: 4000
	})
})
```

Now, what this code does is pretty straightforward. First we are requiring the three Node packages we installed previously, in this case, _Gulp_, _Gulp Ruby Sass_ and _Connect_. Then, we are creating a task that will use the `Connect` module. Every Gulp task you write has essentially the same syntax, the only difference is the task itself, the one that Gulp is going to do for you. 
Let's see.

To create a new task in Gulp you use the `gulp.task()` method. This methods takes two arguments. The first is a string, which is the name you choose for your task. In this case we named it `connect`, so we know what it's doing. The second argument is basically an anonymous function that contains the code required to accomplish the task. In the case of the `connect` task, we are calling the `connect.sever()` method, which takes an options object where we are specifying what's the root of our app, and the port. In this case `public` and `4000` respectively.

Now let's try if it works. Go ahead an create a `public` folder in the root of your project, then create an `index.html` file in it:

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>My App</title>
</head>
<body>
	<h1>It works!</h1>
</body>
</html>
```

Now run the task from terminal

```shell
gulp connect
```

and open your browser on `localhost:4000`. You should see a big heading with the good news. As you see the task is still running on your terminal. To stop the task and thus stop the server, just hit `ctrl + c`.

Now that we have our basic local server setup, lets write some Angular code.

Let's start by adding an `app/main.js` file in the root directory of your project (inside an `app` directory)

```js
require('angular')

var app = angular.module('app', [])

app.controller('MainController', function($scope) {
	$scope.message = 'Angular Works!'
})
```

Cool. But this code won't work because your browser doesn't understand what `require()` does right? So let's use our friend Browserify. 

### Install and create a Browserify task

A few weeks ago, there was a Gulp plugin called `gulp-browserify` which used to do the job. Now it's [deprecated](https://github.com/deepak1556/gulp-browserify) as it was considered redundant by Gulp. That's because the Browserify module itself can be used alone in a Gulp task. Let's see how.

First install Browserify and `vinyl-source-stream`, which is a package that converts a Browserify stream into a stream that Gulp actually understands.

```shell
npm install --save-dev browserify vinyl-source-stream 
```

Then require both of them in your `gulpfile.js`, which should look like this:

```js
var gulp = require('gulp')
var sass = require('gulp-ruby-sass')
var connect = require('gulp-connect')
// requires browserify and vinyl-source-stream
var browserify = require('browserify')
var source = require('vinyl-source-stream')

// Connect task
gulp.task('connect', function () {
	connect.server({
		root: 'public',
		port: 4000
	})
})
```

Now, it's important to know that generally, most of your Gulp tasks will essentially do three things:

1 - It grabs one or many files

2 - It manipulates them

3 - Saves them somewhere

Obviously an exception has been made for the `connect` task which didn't really manipulate any file so far. But that's a rarity.

Now let's write a task that does the above three things: takes your `app/app.js` file, _browserifies_ it and saves it to `public/js/main.js` to be then included in your HTML file.

Append this code to your `gulpfile.js`:

```js
gulp.task('browserify', function() {
	// Grabs the app.js file
    return browserify('./app/app.js')
    	// bundles it and creates a file called main.js
        .bundle()
        .pipe(source('main.js'))
        // saves it the public/js/ directory
        .pipe(gulp.dest('./public/js/'));
})
```

That's it. Now before running this task we have to actually install the angular Node package, because we are including it in the first line of our `app.js` file. That's cool because this way, all you need to do is:

```shell
npm install --save angular
```

Now that you have angular installed as a dependency (note the `--save` option this time) we can go ahead and edit our `public/index.html` file to load our `app.js` file. Also we will need to refactor this file to have an `ng-app="app"` attribute and a controller that shows a message. Your `index.html` file should look like this: 

```html
<!DOCTYPE html>
<html lang="en" ng-app="app">
<head>
	<meta charset="UTF-8">
	<title>My App</title>
	<script src="/js/main.js"></script>
</head>
<body>
	<div ng-controller="MainController">
		<h1>{{ message }}</h1>
	</div>
</body>
</html>
```

Now that our tasks are ready and the Angular dependency are installed, we can go ahead and run the `browserify` task:

```shell
gulp browserify
```

Gulp will log how long it took to do the task. When it's done, you should find a `main.js` file inside of `public/js`.

To recap, this is the folder structure we came up with so far:

![Structure](https://dl.dropboxusercontent.com/u/3105794/blog_images/angular_browserify_gulp/structure.png)

Then let's start our server by running the `connect` task:

```shell
gulp connect
```

Now go to `localhost:4000` and check if Angular is rendering the message `Angular Works!`.

But there is more. Developers are lazy by nature and running tasks manually whenever we make a change to our JS files is less than productive. So let's add a function that fires the `browserify` task whenever a JS file is saved.

Append this to your `gulpfile.js`:

```js
gulp.task('watch', function() {
	gulp.watch('app/**/*.js', ['browserify'])
})

gulp.task('default', ['connect', 'watch'])
```

We've added two new tasks. The first one is a `watch` task which tells Gulp to watch every `.js` file _in any subdirectory_ inside `/app`. Whenever a file is saved, the `browserify` task will run. To be more specific the `gulp.watch()` method takes two arguments. The first is a string with a file pattern, the second is an array of tasks to perform. 

The last task, `default` is a _shortcut_ task. This tells Gulp that if we just run `gulp` in terminal, it should run the `connect` task—therefore start our localhost server—and run the `watch` task which will watch our files and eventually run their associated tasks. With this we've killed two birds (or more than two actually) with one stone.

Now on terminal: 

```shell
gulp
``` 

Go ahead and edit the `$scope.message` value in the `app.js` file like this for example:

```js
$scope.message = 'Two birds killed with one stone!'
``` 

Refresh your browser and boom!.

### Modular Angular

Now that we have set up our basic Gulp tasks, let's take advantage of Node and Browserify and split our code. For example, a good thing to do at this point would be to split our `MainController` controller from the actual `app.js` file. Let's do that. 

First create a `controllers` folder in the `app` directory. The create a `MainController.js` file in there which should look like this:

```js
module.exports = function($scope) {
	$scope.message = 'Two birds killed with one stone!'
}
```

If you're familiar with node, this should be pretty self explanatory. We're actually "exporting" the function we passed earlier in our controller (in `app.js`) from this file. So now our `app.js` file will need to be refactored to this: 

```js
require('angular')
var MainController = require('./controllers/MainController')

var app = angular.module('app', [])
app.controller('MainController', ['$scope', MainController])
```

So, first we are requiring our `MainController` which exports a function and save it to a `var MainController`. Then, in the last line, we are still creating an Angular Controller but instead of passing it an inline function, we are passing the required `MainController` file which _exports_ the function, so the result is the same. Of course the syntax changes a little bit, because of the way Angular handles [Dependency Injection](https://docs.angularjs.org/guide/di).

And from there, you can actually have folders like, `services` or `directives`. Or if your project is big, you can make folders based on features. For example, if your app features a registration page, you can make a folder named `registration` which contains a `registrationController.js`, a `registrationTemplate.html` and a `registrationService.js` file. It really depends on how you want to split your app.

### This is awesome
With this Gulp and Browserify setup, separating your app into sections (see the [Separation of Concerns](http://en.wikipedia.org/wiki/Separation_of_concerns) design principle), becomes easier, because you won't need to include a JS file in your HTML file every time you create a feature. Also, most of the JS libraries you probably use on a daily basis, can be installed with `npm` so you can require them in your code. These include libraries like jQuery, lodash, moment.js, underscore, superagent and more. 

### Compile sass with Gulp

We have already installed our `gulp-ruby-sass` plugin so let's use it. First let's create a `sass` directory in the root of our project and then stick a sass file into it, like `style.sass`. Note that this would work with `.scss` files as well, but I like `.sass` better because it's less cluttered.

```sass
$someColor: #bada55
h1
	color: $someColor
```

Then write a Gulp task:

```js
gulp.task('sass', function() {
	return sass('sass/style.sass')
		.pipe(gulp.dest('public/css'))
})
```

And edit the `watch` task so that it watches the `sass` file as well:

```js
gulp.task('watch', function() {
	gulp.watch('app/**/*.js', ['browserify'])
	// Watches for changes in style.sass and runs the sass task
	gulp.watch('sass/style.sass', ['sass'])
})
```

Now all you need to do is to actually link the `style.css` file that will be generated, into the `<head>` of the HTML file:

```html
<link rel="stylesheet" href="/css/style.css">
```

Now run `gulp` in terminal, make changes to your `.sass` file and observe.

### Conclusion

This approach has drastically changed the workflows of modern front-end development, especially for apps using large frameworks like Angular. The ability to split our app into smaller modules will make it easier to maintain, more scalable and more testable. For your convenience you can find the source code of this tutorial [here](https://github.com/omarfouad/tute_angular_browserify_gulp), and of course, don't hesitate to [reach out](https://twitter.com/omarfouad) if you have any questions.
