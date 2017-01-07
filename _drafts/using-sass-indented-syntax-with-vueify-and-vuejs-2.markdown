---
title: Using Sass indented syntax with Vueify and VueJS 2
date: 2017-01-07 14:59:00 Z
---

Among many CSS preprocessors available these days, SASS is my favorite also because of its indented syntax (.sass files). 
These days I'm rewriting a web app using VueJS 2, using the Browserify template generated through the VueJS CLI. Since this framework is almost entirely based on components, the author provided a way to write a component in one file, which is split into three portions: template, logic and style. These can be written in a single .vue file which contains a template tag, a script tag and a style tag:

