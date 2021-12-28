---
layout: post
title: "JavaScript File Structure and Unification"
date: 2017-01-18
author: ehsan
categories: [JavaScript]
excerpt_separator: <!--more-->
---

In the [Modularizing JavaScript Code](https://ehsankorhani.com/blog/2016/11/13/modularizing-javascript-code) post, I wrote about how to convert plain JavaScript codes into modules. (using Revealing Module Pattern).

We used Revealing Module Pattern, but in fact, there are several patterns for modularizing js codes.

#### File Structure

Modularizing can be done in a single js file, but it\’s recommended to split each module into a separate file. Doing so allows a better control over each module and facilitates working as teams on a project.
<!--more-->
However, as the project grows you will end up with a tremendous amount of js files beside each other. Therefore, you should organize them into a defined directory structure as well as follow a naming convention for the files and folders.

A common convention for naming codes which are responsible for business logics and interacting with DOM is to postfix them with “Controller” word.

for example; customerController.

The code providing specific services such as calling a server API could be postfixed with “Service”.

for example; customerService.

These are borrowed from AngularJs conventions.

#### Folder Structure

Again, there are different views on that.

One approach is to create a folder for all the modules with the same type.

<img src="/images/js-structure-01.jpg" />

Another approach is to separate different sections by their responsibility.

<img src="/images/js-structure-01.jpg" />

The correct choice depends on the nature of the project, however, I personally prefer the 2nd approach.

#### Dependency Order

Despite the fact that JavaScript moves any variable declaration to the top of the page – a behavior that we call Hoisting – still it would be safer to code in order of usage. So, when we add js files to a web page we better place them in an order that later source codes can access former ones.

#### Unification

We learned that splitting JavaScript files into multiple files will make development easier. But one important thing to consider is that many files, means many server requests which is not so pleasant on the server side.

Therefore, we need to unify the JavaScript files.

We would get an even better result if we Minify the files in this process as well.

ASP.NET provides a bundling option which accepts multiple js (or css) files and returns a single minified file instead (when not in debug mode).

Although this is a very convenient method, a more modern approach would be to use some type of JavaScript library to unify the files and manage the dependencies (such as RequireJs, Browserify, …) and minifiers/uglifiers for this reason.
