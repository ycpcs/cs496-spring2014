---
layout: default
title: "Lecture 1: Course overview, client/server systems"
---

/restindex

Web and Mobile Application Development
======================================

Goal for course: become familiar with architecture, design, and implementation of web and mobile applications, including a server component.

Question: what, specifically, do **you** want to do in the course? We're flexible.

Course overview
---------------

-   Semester project: design and implement a client/server system supporting both web and mobile user interfaces. 65% of grade.
-   Individual homework assignments: to gain experience with concepts and techniques we are covering. 30% of grade
-   Attendance and participation: 5% of grade

Client/server systems
=====================

-   Communicate over a network

    -   Often *asynchronously*: sending message, processing on server, and receiving response may be lengthy. Client may need to do other work while transaction is pending.

-   Store and access persistent data

    -   So, there is typically a database involved on the server side.

-   One server may support many clients

    -   Server will handle many requests *concurrently*. Must be careful about accessing and updating shared data.

-   One server may not suffice if there are a large number of clients!

    -   May need to *replicate* servers: any server may handle a request from any client.
    -   Issue: load balancing.

-   We typically want *high availability*

    -   Try to avoid the failure of any single component of the system causing the entire system to fail.
    -   Must build *redundancy* into the system, so that when a component (e.g., a server) fails its work can be transparently handled by other components.
    -   This is a tough problem when you have a central database. It becomes a central point of failure. Clustered (redundant) databases can be employed.

Web services and applications
-----------------------------

Historically, lots of custom/specialized protocols were used to implement client/server systems.

Today, the web, and its protocol, HTTP, are used predominantly.
