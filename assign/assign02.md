---
layout: default
title: "Assignment 2: Mobile Inventory Client"
---

**Due:** Tuesday, Feb 25th by 11:59 PM

This is an **individual** assignment

Getting Started
===============

Download [CS496\_Assign02.zip](CS496_Assign02.zip) and import into Eclipse. 

This assignment consists of creating a mobile client to access the inventory web service created in [Assignment 1](assign01.html). Hence you will want to use the same model objects in order to maintain JSON compatibility.

Your Tasks
==========

Your tasks involve:

-    Creating a mobile UI that allows the user to enter relevant information to either retrieve, modify, or delete items from the inventory.
-    Add corresponding mobile controllers to send RESTful API operation requests to the server.
-    Process any appropriate responses from the server to be displayed in the mobile app.

Implementing the Mobile Client
------------------------------

An example mobile client UI might create:

-   two **EditText** components allowing the user to enter an item name and quantity
-   four **Button** components allowing the user to send either a **GET**, **PUT**, **POST**, or **DELETE** request using the information from the **EditText** boxes
-   one **Button** component to show the entire current inventory stored on the server

You may choose to enhance the UI if you wish.

> ![image](images/assign02/defaultLayout.png)

The requirements for the mobile client are that it should retrieve the information from the **EditText** boxes (i.e. the item name and quantity) and produce the appropriate request based on the button clicked as follows:

-   **Get** - if no item name is entered in the **EditText** box, the app should display the entire inventory (**not** as JSON) using the same *dynamic* layout as **Show Inventory** described below. If an item name is entered in the **EditText** box, the app should create a **GET** URI to retrieve *just that item* (if it exists in the inventory) and display its current quantity using the same *dynamic* layout as **Show Inventory** described below but with only a single item.
-   **Put** - should require that an item name and quantity are in the **EditText** boxes, create a valid JSON object for the item (see the Hints below), and send a **PUT** request with the JSON as the *payload* to *update* the specified item.
-   **Post** - should require that an item name and quantity are in the **EditText** boxes, create a valid JSON object for the item, and send a **POST** request with the JSON as the *payload* to *add* the new item.
-   **Delete** - if no item name is entered in the **EditText** box, the app should delete the *entire* inventory. If an item name is entered in the **EditText** box, the app should create a **DELETE** URI to delete just that item (if it exists in the inventory).
-   **Show Inventory** - should display the entire current inventory from the server in a new *dynamic* layout that contains a **Back** button to return the user to the original data entry view.

Since **PUT**, **POST**, and **DELETE** do not inherently generate output, display a **Toast** message informing the user as to what action has been performed (and if it was successfully completed)

> ![image](images/assign2/defaultOutput.png)

The inventory layout should look like

> ![image](images/assign2/inventoryOutput.png)

Hints
-----

To access *localhost* through the Android emulator, you need to use the IP address 10.0.2.2.

Use separate controller objects for each of the REST requests (similar to server side) that are called from the corresponding button click callbacks. In each class, have a method which instantiates the appropriate **HttpGet**, **HttpPut**, **HttpPost**, or **HttpDelete** object.

Create two methods for setting the layout - one that loads the initial layout from **activity_main.xml** and the other which creates a dynamic **LinearLayout** view. Call each of these methods as needed from button click methods. Have the *dynamic* layout method take an **Inventory** parameter (allowing it to be reused for displaying either the entire inventory retrieved from the server or a partial inventory consisting of just a single item).

Grading
=======

Your grade will be assigned as follows:

-   Original input layout: 10%
-   Dynamic output layout: 10%
-   Get - 30%
-   Put - 20%
-   Post - 20%
-   Delete - 10%

Submitting
==========

Select the **CS496\_Assign02** project, then click the blue up arrow icon, and enter your Marmoset username and password when prompted.

Alternatively, export the **CS496\_Assign02** project to a zipfile, and upload the zipfile to Marmoset as **assign02**:

> [https://cs.ycp.edu/marmoset](https://cs.ycp.edu/marmoset)

<div class="callout">
<b>Important</b>: please do <b>not</b> submit the <b>CS496_Jetty</b> project as part of your submission.  Only submit <b>CS496_Assign02</b>.
</div>

<!-- vim:set wrap: ­-->
<!-- vim:set linebreak: -->
<!-- vim:set nolist: -->
