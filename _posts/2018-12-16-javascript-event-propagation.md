---
layout: post
title: "JavaScript Event Propagation"
date: 2018-12-16
author: ehsan
categories: [JavaScript]
excerpt_separator: <!--more-->
---

JavaScript can handles events in two different ways:
1. Phase 1: Event Capturing
2. Phase 2: Event Bubbling

<img src="/images/js-event-capture-bubble.png" alt="Event Bubbling and Event Capturing (https://medium.com/@vsvaibhav2016/event-bubbling-and-event-capturing-in-javascript-6ff38bec30e)" />

<!--more-->

##### Event Capturing
In this type of event propagation the event starts from the root node and reverse the DOM until it reaches the element that has raised the event. 

##### Event Bubbling
Propagation starts from target element and bubbles up until it reaches the root node. This is the default behaviour of `addEventListener`.

#### How does it work?
To demonstrate this functionality, let's first create a sample nested document:

```html
<!DOCTYPE html>
<html>
<head>   
    <link rel="stylesheet" type="text/css" href="main.css" />
</head>
<body>
    <div id="section-outer" style="background-color:navy;">
        <div id="section-middle" style="background-color:yellow;">
            <div id="section-inner" style="background-color:violet;">
                <button id="btn">Click Me!</button>
            </div>
        </div>
    </div>
    <script src="main.js"></script>
</body>
</html>
```

and in the `main.js` file we add the listeners:

```javascript
var elOuter = document.getElementById('section-outer');
var elMiddle = document.getElementById('section-middle');
var elInner = document.getElementById('section-inner');
var btn = document.getElementById('btn');

elOuter.addEventListener("click", function(e) {
    console.log('Event captured in outer section.');    
}, true);

elMiddle.addEventListener("click", function(e) {
    console.log('Event captured in middle section.');  
}, true);

elInner.addEventListener("click", function(e) {
    console.log('Event captured in inner section.');  
}, true);

btn.addEventListener("click", function(e) {
    console.log('Button was clicked:', e.target);
}, true);
```

if we click the button, we will get:

```bash
Event captured in outer section.
Event captured in middle section.
Event captured in inner section.
Button was clicked: <button id=​"btn">​Click Me!​</button>​
```

This is because the event was `Captured` from the root to the target.
However if we change the third argument to false (which is the default and is equal to not defining it):

```javascript
...
elOuter.addEventListener("click", function(e) {
    console.log('Event captured in outer section.');    
}, false);

elMiddle.addEventListener("click", function(e) {
    console.log('Event captured in middle section.');  
}, false);

elInner.addEventListener("click", function(e) {
    console.log('Event captured in inner section.');  
}, false);

btn.addEventListener("click", function(e) {
    console.log('Button was clicked:', e.target);
}, false);
```

The result will be:

```bash
Button was clicked: <button id=​"btn">​Click Me!​</button>​
Event captured in inner section.
Event captured in middle section.
Event captured in outer section.
```

This time the event has `Bubbled` from target to root.

#### Stop Propagation
This function blocks an event from capturing or bubbling further more.

If we take the last example and add this to one of the listeners:

```javascript
...
elOuter.addEventListener("click", function(e) {
    console.log('Event captured in outer section.');    
}, true);

elMiddle.addEventListener("click", function(e) {
    e.stopPropagation();
    console.log('Event captured in middle section.');  
}, true);

elInner.addEventListener("click", function(e) {
    console.log('Event captured in inner section.');  
}, true);

btn.addEventListener("click", function(e) {
    console.log('Button was clicked:', e.target);
}, true);
```

The output will be:

```bash
Event captured in outer section.
Event captured in middle section.
```

The `e.stopPropagation()' prevented the event _capturing_ after being set inside the _middle_ section.

on the opposite type:

```javascript
...
elOuter.addEventListener("click", function(e) {
    console.log('Event captured in outer section.');    
}, false);

elMiddle.addEventListener("click", function(e) {
    e.stopPropagation();
    console.log('Event captured in middle section.');  
}, false);

elInner.addEventListener("click", function(e) {
    console.log('Event captured in inner section.');  
}, false);

btn.addEventListener("click", function(e) {
    console.log('Button was clicked:', e.target);
}, false);
```

The system will log:

```bash
Button was clicked: <button id=​"btn">​Click Me!​</button>​
Event captured in inner section.
Event captured in middle section.
```

This is particularly helpful when place it on the target element event handler itself when we don't want any other `eventListener` to handle the event in the default bubble phase.

```javascript
btn.addEventListener("click", function(e) {
    e.stopPropagation();
    console.log('Button was clicked:', e.target);
});
```
