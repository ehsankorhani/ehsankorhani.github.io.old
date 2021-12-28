---
layout: post
title: "Modularizing JavaScript Code"
date: 2016-11-13
description:
author: ehsan
categories: [JavaScript]
excerpt_separator: <!--more-->
---

#### Global Namespace Pollution
All JavaScript codes can run inside Global Execution Context.
This means that if we define variables and functions at the global level, then the other codes can access them, no matter where they are and even if they are in different files.

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <script type="text/javascript" src="file1.js"></script>
        <script type="text/javascript" src="file2.js"></script>
    </head>
    <body>
        <!-- body -->
    </body>
</html>
```
<!--more-->
file1.js:

```javascript
var country = "Australia";
```

file2.js:

```javascript
console.log(country);
```

even defining a “use strict” in one file can affect others as well.

However, variables defined inside a function are not accessible from other codes. Thus, a number of patterns have been proposed to overcome the issue.

#### Revealing Module Pattern

The pattern simply consist of a Immediately-Invoked Function Expression:

```javascript
var customerModule = (function () {

    var type = "Uncategorized";

    var list = function () {
        // code...
    };

    return {
        list: list
    }

})();
```

Any outsider can only access those variables the module which has been exposed through return statement. So, no any other code can get the “type” of “customerModule”, but the can receive the return value of “getList” function.

This pattern helps us in implementing Single-Responsibility principle. Therefore, we can create various modules that are responsible for doing a specific job and let them access other module services by a kind of dependency injection.

```javascript
var CustomerService = (function () {

    var getList = function (data, done, fail) {
        $.post("/api/customers", data)
            .done(done)
            .fail(fail);
    }

    return {
        getList: getList
    }

})();

var customerModule = (function (customerService) {

    var type = "Uncategorized";

    var done = function (data) {
        // code...
    }

    var fail = function (xhr, textStatus, errorThrown) {
        // code...
    }

    var list = function () {
        customerService.getList({
            type: type
        }, done, fail);
    };

    return {
        list: list
    }

})(CustomerService);
```
