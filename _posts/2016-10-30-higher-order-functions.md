---
layout: post
title: Higher-order Functions
description: >
  An introduction to functional programming.
author: ehsan
categories: [JavaScript]
excerpt_separator: <!--more-->
---

In order to write great code in JavaScript or even understand frameworks written in this language, it’s vital to understand the meaning of “Higher-order Functions”.

> Functions that operate on other functions, either by taking them as
> arguments or by returning them, are called higher-order functions.
> 
> **Eloquent JavaScript**
<!--more-->
Or simply; in JavaScript, we can pass functions as an argument to other functions and receive a function as a result of their execution.

```javascript
var employees = [{
    name: 'Alfie Atkins',
    position: 'Programmer'
}, {
    name: 'Torill Kove',
    position: 'Director'
}];

var programmers = employees.filter(function (employee) {
    return employee.position === 'Programmer'
});
```
After all, unlike OOP languages, Functions in JavaScript can exist on their own without any dependency on a class. That’s why we call them “first-class citizens”. They are objects (just like OOP class instances), and objects can be assigned to variables.

Therefore to make things even simpler, let’s say that: in JavaScript, functions are values (just like numbers and strings).

```javascript
var foo = function () {
    console.log('Hello World!');
}

foo();
```
