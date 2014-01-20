---
layout: default
title: "Lecture 3: RESTful Web Services"
---

REST
====

REST = REpresentational State Transfer.  The concept was developed by
[Roy Fielding](http://en.wikipedia.org/wiki/Roy_Fielding) in his
[Ph.D. dissertation](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm).
REST describes a set of architectural principles
for designing distributed systems.

REST principles could be applied to any distributed system, but in
practice it is used most often for web applications and services,
where clients and servers communicate using the HTTP protocol.

The basic idea is pretty simple:

* There are *resources*.  Each resource has a unique name.
  In the context of the web (and the HTTP protocol),
  each resource is named by a URL.
  The URLs specify the server and "path" of the resource.
  The server part of the URL specifies which server to contact
  to access the resource.  The path part of the URL names
  the resource to distinguish it from other resources on
  the same server.
* There are *representations* of resources.  For any resource,
  a web application might present it in a variety of formats.
  For example, the same resource could be represented using HTML
  and CSS for presentation in a web browser, and also by
  XML or JSON for use by programs.
* There are *verbs* which clients can use to create, access, and modify
  resources.   In the context of the web, the verbs are the
  HTTP request methods.  The most common HTTP methods are **GET**, **PUT**, **POST**, **DELETE**.
  Each HTTP method 

We'll use a concrete example: a wholesale fruit business, called
**ycpfruit.com**.

Naming resources
----------------

One important kind of resource is the kinds of fruit that are available.
For example, the business might make the list of the kinds of fruit it sells
available at the URL

> `http://ycpfruit.com/fruit`

More information about specific kinds of fruit could be made
available through URLs like

> `http://ycpfruit.com/fruit/Apples`

> `http://ycpfruit.com/fruit/Oranges`

It is common to organize resource URLs in a directory-like scheme:
for example, we can think of the path `/fruit` as naming fruit in
general, and more specific paths such as `/fruit/Apples` as naming
specific kinds of fruit.

Verbs
-----

In a RESTful web service, the HTTP actions (a.k.a. methods) are used
as verbs to create, access, and modify resources.

The **GET** verb accesses a resource: in other words, it requests that
the web service return a representation of the named resource.

The **POST** verb creates a resource.  For example, a **POST** request
to the path `/fruit`, where the body of the request contains a
representation of a fruit resource (perhaps in XML or JSON format),
would create a new fruit resource (which could then be accessed by
subsequent requests).

The **PUT** verb creates a new resource or replaces an existing resource.
For example, a **PUT** request
to the path `/fruit/Apples` would replace the `Apples` resource
with the one contained in the request body (assuming that a
resource named `Apples` existed previously).

The **DELETE** verb deletes the resource named by a resource.
For example, a **DELETE** request specifying the path `/fruit/Kumquats`
would delete `Kumquats` as a fruit, and no further requests to
access that resource would succeed.

TODO: persistence layer (implementation using in-memory data structures)
