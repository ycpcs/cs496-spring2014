---
layout: default
title: "Lab 14: Real Persistence"
---

Getting Started
===============

Import [CS496\_Lab14.zip](CS496_Lab14.zip) into Eclipse (**Import &rarr; General &rarr; Existing Projects into Workspace &rarr; Archive File**).

You should see a project called **CS496\_Lab14**.

Real Persistence
================

The project you imported is a combination of [Assignment 1](../assign/assign01.html) and [Assignment 4](assign/assign04.html).  Specifically, it is *both* a web service and a web application.  (It's a floor wax *and* a dessert topping.)

The only difference between the lab and the assignments it is based on is that it has a new implementation of the **IDatabase** interface called **SqliteDatabase**, which (as specified in the **Database** class) is the implementation that will be used when the program runs.

Start by executing **SqliteDatabase** as a Java application.  You should see the following output:

	Creating tables...
	Loading initial data...
	Done!

Refresh the Eclipse project.  You should see a file called **test.db**.  This is the database!  It allows the inventory data to persist between runs of the application.

Now, run the **Main** class as a Java application.  Navigate to

> [http://localhost:8081/app/inventory](http://localhost:8081/app/inventory)

to see the UI for the web application.  Note that adding items is supported &mdash; try it out!

Your Task
=========

Add support to the web application for editing items and deleting items.

Add support to the web service for the various REST operations.  Note that the URL for the web service will be

> [http://localhost:8081/svc/inventory](http://localhost:8081/svc/inventory)

You should be able to reuse your controller, servlet, and JSP code from the assignments.
