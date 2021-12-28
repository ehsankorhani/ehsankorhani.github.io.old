---
layout: post
title: "Designing a Web API – Introduction"
date: 2016-11-22
author: ehsan
excerpt_separator: <!--more-->
---

These days, having back-end as a service and then using one sort of JavaScript frameworks have become a trend in developing web applications.

Apart from that, many service providers have had their SOAP web services to communicate with their customers and some are re-designing their API in REST-ful manner. Following a correct approach in designing the API is an important aspect of application architecture because consumers should be able to use the exposed api in a somehow standard way.
<!--more-->
#### REST and CRUD operations

REST uses HTTP calls. So, as a standard definition, we map HTTP methods to usual CRUD operations.

* GET:    read or select
* POST:     create or insert
* PUT:    update
* DELETE:    delete

#### URI Design Principles

* URI’s should contain Nouns: http://www.server.com/api/employees
having Verb is incorrect: http://www.server.com/api/GetEmployees
* Include a parameter to return an individual item: http://www.server.com/api/employees/2999

#### What do we return?

* .../api/employees
  * Get:    List of items
  * Post:    Create a new item and return the item or id or status code
  * Put:    Update all items and return status code
  * Delete:    Status code of error, since we should not allow a batch delete)
* …/api/employees/2999
  * Get:    Item
  * Post:    Error status code, since we cannot create an existing item
  * Put:    Updated item
  * Delete:    Status code

#### What status code to return?

It is more convenient to return HTTP status code in response to HTTP method calls as well. There are many status codes defined for HTTP. However, we usually require between 3 to 10 of those codes.

the most frequent codes being used are:

* 200: OK
* 201: Created
* 304: Not Modified
* 400: Bad Request
* 401: Unauthorized
* 403: Forbidden
* 404: Not found
* 500: Internal Server Error
