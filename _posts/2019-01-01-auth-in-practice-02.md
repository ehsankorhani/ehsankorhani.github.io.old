---
layout: post
title: "Authentication and Authorization in Practice – Chapter 2"
date: 2019-01-01
author: ehsan
categories: [Web Security]
excerpt_separator: <!--more-->
---

#### Delegated Authorization
In the previous [article](https://ehsankorhani.com/blog/2018/12/15/auth-in-practice-01) we discussed the problem of a client application accessing user data in a resource server.

<img src="/images/security-httpBasicAuthentication.gif" alt="Diagram of four steps in HTTP basic authentication between client and server (docs.oracle.com)" />

<!--more-->

It was simply the steps to:
1. Request a resource from a server
2. Being redirected to login page
3. Enter username and password

    ```bach
    GET /login/ HTTP/1.1
    Host: www.ehsankorhani.com
    Authorization: Basic aAJ0cKfgdGCoObE=
    ```

4. Server will lookup user info
5. Get access to protected page.
   - Normally in the form of a Cookie
   - Browser will send this Cookie in each subsequent request
   `Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;`


The encrypted text in authorization header is just a:
`base64Encode(username:password)`

Providing basic authentication requires the back-end server to implement a user database with proper hashing and security measures.
[ASP.NET Identity](https://www.asp.net/identity) is one of the Microsoft technologies to simplify this task.


##### Other Scenarios
So, basic authentication could solve the simple and direct user interaction with the _Resource Server_.
But it cannot be very effective in these cases:
1. Authenticating using accounts with other Identity providers.
2. Delegating access and authorization to another application.

#### Delegated Authorization & Federated Authentication
It could be cumbersome for users to create an account for every application they use. Securing user info in database is another burden for owners of application.

In addition, how can we allow an application to lookup our data in a reource server or perform actions on our behalf?

These are the scenarios that Delegated Authorization & Federated Authentication are going to address.
