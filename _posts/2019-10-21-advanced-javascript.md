---
layout: post
title: "Advanced JavaScript"
date: 2019-10-21
author: ehsan
categories: [JavaScript]
excerpt_separator: <!--more-->
---

<img src="/assets/images/js-code-01.png" />

This article is going to discuss a more advanced topics of JavaScript language. This is journey to examine the different, wierd or specific features of JavaScript and also to practice the better ways to coding.

**Table if Contents**
* [Null vs Undefined](https://ehsankorhani.com/javascript/2019-10-21-advanced-javascript/#null-vs-undefined)
* [Var vs Let vs Const](https://ehsankorhani.com/javascript/2019-10-21-advanced-javascript/#var-vs-let-vs-const)
* [Type conversion with operators](https://ehsankorhani.com/javascript/2019-10-21-advanced-javascript/#type-conversion-with-operators)
* [Comparison & Strict equality](https://ehsankorhani.com/javascript/2019-10-21-advanced-javascript/#comparison-and-strict-equality)

<!--more-->

##### Null vs Undefined
**Best practice** is to never assign ```undefined``` to a variable and use it only to check if a variable has been initialized or assigned or not.

In many OOP languages such as C#, a variable cannot be declared without being initialized.
```csharp
/* C# */
var foo;  // compile error
var bar = null; // compile error
string baz = null; // ok
```

Here ```null``` is a "reference to a non-existing object" or a "null pointer".

However, this is completely possible in JavaScript.
If no value has been assigned to a variable, it will be have the ```undefined``` value.

```javascript
/* JS */
var foo;  // = undefined
var bar = undefined;  // = undefined
var baz = null;  // = null
```

In the above code, ```undefined``` means no value has been assigned, while ```null``` is just a special falsy value.

In addition while ```null``` is an _object_, ```undefined``` is of _undefined_ type.

```javascript
var nullType = typeof null;  // "object"
var undefinedType = typeof undefined;  // "undefined"
```

Keep in mind that while ```null``` is an (special) _object_, it evaluates as ```false```.

---
##### Var vs Let vs Const
**Best practice** Always use ```const```. Start declarations with ```const```. Change it to ```let``` if later on you found out that the value needs to be changed.

Prior to ES6, a JavaScript code could have been written in _no strict mode_. Meaning, there was no need to declare a variable. However, this was not recommended, and the programmers were advised to write ```'use strict';``` at top of code or module.

from ES6 onward the JavaScript is always in strict mode. ```let``` and ```const``` were also standardized with ES6.

```const``` is obviously used to declare _constants_. And ```let``` is almost like ```var```. Almost, because there is an slight difference between these two keywords:

* Hoisting

Variables declared with ```var``` hoisted to top of the function.

```javascript
function greeting(name) {
  console.log(phrase, 'at top.');

  var phrase = `Hello ${name}`;

  console.log(phrase, 'at bottom.');
}

greeting('Alfie Atkins');
```
will print:
```bash
undefined "at top." # because declarations are hoisted, but assignments are not.
Hello Alfie Atkins at bottom.
```

but, 

```javascript
function greeting(name) {
  console.log(phrase, 'at top.'); // ReferenceError

  let phrase = `Hello ${name}`;

  console.log(phrase, 'at bottom.');
}

greeting('Alfie Atkins');
```


* ```var``` does not have a block scope.

Therefore, a variable declared inside an ```if``` statement or ```for``` loop can be accessed outside of that block.

```javascript
if (true) {
  var fname = 'Alfie';
  let lname = 'Atkins';
}

console.log(fname); // Alfie
console.log(lname); // Uncaught ReferenceError: lname is not defined
```

This is the real reason why ```let``` was introduced instead if old ```var```.

**Note:**

- Constant cannot change through re-assignment
- Constant cannot be re-declared

It creates a _read-only reference_ to a value.

But the value is still mutable:

```javascript
const obj = {};
const arr = [];

obj.foo = 'some value';
arr.push('another value');
```

---

#### Type conversion with operators

Numeric conversion happens in mathematical functions and expressions automatically:

```javascript
console.log('10' - '5'); // 5
console.log('10' * '5'); // 50
console.log('10' / '5'); // 2

console.log('10' - 5); // 5
console.log(10 * '5'); // 50
```

Things are different when using ```+``` to add values:

```javascript
console.log(10 + 5); // 15
console.log('10' + 5); // 105
console.log(10 + '5'); // 105
console.log('10' + '5'); // 105
```

Because ```+``` is a string concatenation operator and converts integers to string in the examples above.

However it can be used to convert string into integer as well:

```javascript
console.log(+'10'); // 10 (int)
```

Weird but useful!

---

#### Comparison and Strict equality
**Best practice**: Always try to use ```===``` and avoid greater or lesser comparison when variable can have ```null``` or ```undefined``` values.

Strict equality (and inequality) was added later to JavaScript in a response to troubles with normal ```==``` operator.
This operator along with ```!=```, ```>```, ```>=```, ```<``` and ```<=``` perform a type cast before the operation and this can lead to unexpected behaviors.

**```null``` casts to 0**
```null``` is not equal to 0 in simple equality:
```js
null == 0; // false
```

but gets converted to 0 in lesser or greater comparison:
```js
null > 0;  // false
null >= 0; // true
```

**```undefined``` casts to ```NaN```**
```javascript
undefined == 0; // false
```

but gets converted to 0 in lesser or greater comparison:
```js
undefined > 0;  // false
undefined < 0; // false
```


---

##### References

[The Modern JavaScript Tutorial](http://javascript.info/) <br />
[You Don't Know JS Yet (book series) - 2nd Edition](https://github.com/getify/You-Dont-Know-JS) <br />
[Eloquent JavaScript](http://eloquentjavascript.net/) <br />
[DOM Enlightenment](http://domenlightenment.com/) <br />
