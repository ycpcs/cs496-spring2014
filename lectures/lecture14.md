---
layout: default
title: "Lecture 14: Databases, SQL, JDBC"
---

Databases, ORM
==============

We will be using databases to load and store objects.

Relational database: data is organized as tables. Each *tuple*, or *row*, of the table is one record. For our purposes, each row is one object. So, it is important to keep in mind that the structure of the tables in the database directly follows the data we have defined as fields in our model classes. When a relational database is used to store objects, we need an *object-relational mapping* (ORM) to define how objects are stored to and loaded from a table in the database.

For example, in the **items** table, each row represents one **Item** object.

Database tables have *columns*, which define the data values stored in each row. Each column has a datatype, which should match the Java datatype of the corresponding field of the kind of object that is going to be stored in the table.

Examples of SQL datatypes.

Unique ids, autoincrement columns.

Indexes, can optimize particular data-access patterns (e.g., select item by name), and enforce data integrity (don't allow two items with the same name.)

Example: **items** table:

    create table items (
      id integer primary key autoincrement,
      name varchar(80) unique,
      quantity integer
    )

Some more complete notes on relational databases and SQL:

* [Relational Databases](http://ycpcs.github.io/cs320-spring2014/lectures/lecture11.html)

SQL
===

Language for retrieving, storing, updating, deleting data in a relational database.

-   **select** - select rows of table(s) matching specified criteria
-   **insert** - insert new row(s) in table
-   **update** - modify row(s) of table matching specified criteria
-   **delete** - delete row(s) from table

Google is your friend for learning SQL.

Transactions
============

Your application will often need to ensure that a sequence containing several operations executes *atomically*. For example, to modify the data for an object in the database, the program might

1.  **select** to locate the object's data in the database
2.  modify the data in-memory
3.  **update** to replace the object's data in the database

For this sequence to be correct, it must not be possible for any other task to modify the object's data in the database between steps 1 and 3. In other words, steps 1 through 3 must take place *atomically*.

Question: what could happen if 1-3 were not executed atomically?

Relational databases support *transactions* to ensure that a sequence of operations is executed atomically.

JDBC
====

Execute SQL from a Java program.

-   **Connection** - represents a connection to the database server
-   **PreparedStatement** - represents a statement (select, insert, update, delete, etc.). If the statement contained *placeholders*, the **PreparedStatement** allows you to *bind* the placeholders to values determined by the program.
-   **ResultSet** - a collection of 0 or more rows returned by a query.

More complete notes on JDBC:

* [Database Applications, JDBC](http://ycpcs.github.io/cs320-spring2014/lectures/lecture12.html)

ORM in Java
===========

Create an interface called **IDatabase**. It should define all of the methods involved in retrieving objects from the database, storing objects to the database, updating objects in the database, etc.

> This interface will allow you to implement other persistence mechanisms in the need arises - for example, persistence to files or an embedded database on a mobile device.

Each method can throw an exception (e.g., **PersistenceException**) to signal that a database operation did not complete normally.

Create a concrete class which implements **IDatabase** and uses JDBC to implement loading objects from the database, storing objects to the database, etc.

Some implementation issues:

-   Transactions. Solution: **Transaction** interface. Write a method which executes the **execute** method of an arbitrary **Transaction** in a transaction. The transaction is retried automatically if a deadlock occurs.
-   Resource cleanup: each **PreparedStatement** and **ResultSet** object created must be closed. Solution: always use **try**/**finally** blocks to ensure these resources are closed. Implement a **DBUtil.closeQuietly** method for each resource type: these methods should gracefully handle a **null** reference, and should handle any exception that might be thrown in attempting the close the resource.
-   Retrieving object data from a **ResultSet**. Solution: **load** methods, which take an object and a **ResultSet**, and populate the object's fields with the tuples values currently stored in the **ResultSet**.
-   Storing object data to the database. Solution: **storeNoId** methods, which bind an object's field values to placeholders in an **insert** or **update** statement.
-   Unique ids. When an object is added to the database for the first time, the database will generate a unique id for it (assuming you used an autoincrement **id** column.) Solution: JDBC can return a result set containing the generated keys for all objects inserted. The **storeNoId** methods do not store the object's id value, because it will be autogenerated.

Example ORM in Java (Lab 14)

More complete notes on ORM:

* [ORM, Designing a Persistence Layer](http://ycpcs.github.io/cs320-spring2014/lectures/lecture13.html)
