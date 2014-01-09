---
layout: default
title: "Lecture 2: Web services"
---

HTTP
====

HTTP is the HyperText Transport Protocol. It is the network protocol used by web clients (such as browsers) and web services.

HTTP is a very simple protocol, based on *requests* and *responses*.

A client sends a request to a server, and the server sends back a response. That's really all there is to it!

HTTP requests consist of an *action*, a *resource identifier*, *headers*, and (optionally) a *body*.

-   The action describes the kind of request. Examples of actions are GET (retrieve a resource), PUT (send a resource), DELETE (delete a resource), etc.
-   The resource identifier describes what resource on the server is being accessed.
-   The headers provide metadata about the request. For example, if a PUT action is being done, the Content-Type header would describe what kind of data is being sent.
-   The body is the information payload of the message. For example, if the client is using PUT to send an image, then the body would contain the binary image data.

HTTP responses consist of a *response code*, headers, and a body. The headers and body serve the same purpose as in an HTTP request. The response code is a standard numeric code describing the meaning of the response. For example, the code 200 indicates a normal (success) respose. The code 404 means that a requested resource was not available. (You have probably seen an "404 not found" error which using a web browser, often caused by a broken link on a website.)

Although HTTP is often used to deliver human-readable content (such as HTML pages, images, etc.), it can be used to transport any kind of information.

Web Services
============

Web services, as the name implies, provide a service to client applications "over the web", meaning through the exchange of HTTP messages.

Examples of web services:

-   given address, determine GPS coordinates (geocoding)
-   process a credit card transaction
-   given search criteria, return list of matching items (e.g, for an online store)
-   given a program, compile the program and return an executable

Although in principle you could access a web service through a web browser, in practice it is more common for an application (program) to make use of a web service.

Many (but certainly not all) web services encode information using XML.

Standards for Web Services
--------------------------

Read: [Understanding Web Services](http://www.alistapart.com/articles/webservices/) by Patrick Cooney.

SOAP is a **very complicated** standard for defining web services. A web service implemented using SOAP has a specification: essentially, what kinds of XML messages are accepted as requests by the web service, and what kinds of XML messages are returned as responses by the web service. These specifications are detailed enough that they can be translated into an API callable from a programming language (e.g., Java) using classes and objects.

Web 2.0 APIs
------------

Many web developers feel that SOAP is overkill for many (most?) web services. (Anecdote: I once compiled a description of a SOAP web service into Java and it generated more than 70,000 lines of source code!)

Another style of web service has since grown up, which are sometimes known as Web APIs.

A Web API can be very simple:

-   It can take input using query parameters, or in the body of a message (such as PUT or POST)
-   It can take input in any convenient format: XML, JSON (JavaScript Object Notation, ...)
-   It produces output using any convenient format: XML, JSON, plain text, etc.

Example:

> <WebApp.zip> (an Eclipse project implementing a web server)

**ExampleServlet** is a Java servlet. It handles HTTP requests (GET and POST) and sends back an HTML document (type "text/html") as a response.
