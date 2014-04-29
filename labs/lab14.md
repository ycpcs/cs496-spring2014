---
layout: default
title: "Lab 14: Real Persistence"
---

# Getting Started

Import [CS496\_Lab14.zip](CS496_Lab14.zip) into Eclipse (**Import &rarr; General &rarr; Existing Projects into Workspace &rarr; Archive File**).

You should see a project called **CS496\_Lab14**.

## Update: Derby version

Here is a slightly modified version that uses [Apache Derby](http://db.apache.org/derby/) rather than SQLite:

> [CS496\_Lab14\_Derby](CS496_Lab14_Derby.zip)

It depends on the following Eclipse project (which contains the Derby jar files):

> [CS496\_Derby.zip](CS496_Derby.zip)

Derby is a better choice than SQLite for server applications where multiple clients will be making requests concurrently.

# Real Persistence

The project you imported is a combination of [Assignment 1](../assign/assign01.html) and [Assignment 4](assign/assign04.html).  Specifically, it is *both* a web service and a web application.  ("It's a floor wax *and* a dessert topping.")

The only difference between the lab and the assignments it is based on is that it has a new implementation of the **IDatabase** interface called **SqliteDatabase**, which (as specified in the **Database** class) is the implementation that will be used when the program runs.

> Note that the Derby version uses a **DatabaseProvider** class, where the **IDatabase** instance is set by a **DatabaseInitListener** configured in the `web.xml` file.  This approach is more flexible than the one used in the original version of the lab.

Start by executing **SqliteDatabase** as a Java application (or **DerbyDatabase** if you are using the Derby version).  You should see the following output:

	Creating tables...
	Loading initial data...
	Done!

Refresh the Eclipse project.  You should see a file called **test.db**.  This is the database!  It allows the inventory data to persist between runs of the application.

Now, run the **Main** class as a Java application.  Navigate to

> [http://localhost:8081/app/inventory](http://localhost:8081/app/inventory)

to see the UI for the web application.  Note that adding items is supported &mdash; try it out!

# Your Task

Add support to the web application for editing items and deleting items.

Add support to the web service for the various REST operations.  Note that the URL for the web service will be

> [http://localhost:8081/svc/inventory](http://localhost:8081/svc/inventory)

You should be able to reuse your controller, servlet, and JSP code from the assignments.
