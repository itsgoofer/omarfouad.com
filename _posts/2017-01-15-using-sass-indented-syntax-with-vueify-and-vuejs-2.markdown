---
title: Using Sass indented syntax with Vueify and VueJS 2
date: 2017-01-15 14:59:00 Z
color: "#49d070"
layout: article
---

Among many CSS preprocessors available these days, [SASS](http://sass-lang.com/) is definitely my favorite especially because of its indented syntax (.sass files).

These days I'm rewriting a web app using [VueJS 2](https://vuejs.org/), using the Browserify template generated through the [VueJS CLI](https://github.com/vuejs/vue-cli). Since this framework is almost entirely based on components, the author provided a way to write a component in one file, which is split into three portions: template, logic and style. These can be written in a single `.vue` file which contains a template tag, a script tag and a style tag:

```
<template>
  <h1>Hello, World!</h1>
</template>

<script>
  export default {
    name: "HeaderComponent"
  }
</script>

<style>
  h1 {
    color: #bada55
  }
</style>
```

It's important to note that the Vue ecosystem includes a Browserify plugin (transform) that handles these `.vue` files and it's called [Vueify](https://github.com/vuejs/vueify), which is installed with the other NPM packages when a project is bootstrapped using the official Vue CLI using the [Browserify template](https://github.com/vuejs-templates/browserify). 

Now, in the `style` portion of your `.vue` file, you can write SASS just by adding a `lang` attribute to the tag: 

```
<style lang="sass">
  h1
    color: #bada55
</style> 
```

However, in order for this to work, we have to make a couple of changes to our projects. Assuming you have the SASS Ruby Gen installed on your system, we first need to install the `node-sass` package: 

```
npm install --save-dev node-sass
```

The next step is to create a file named `vue.config.js` at the root of our project directory (if it's not there already), with the following code: 

```
module.exports = {
  sass: {
    includePaths: ['./src/sass'],
    indentedSyntax: true
  }
}
```

Note the `indentedSyntax` property set to `true`. This tells Vueify that our sass syntax is, obviously, the indented one. Of course if we set it to `false`, or just don't set it at all, Vueify will compile SASS written in its normal syntax (the one using braces and semicolons).

Don't hesitate to contact me if you have questions. 
