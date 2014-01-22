---
layout: default
title: "Lecture 5: RESTful mobile requests"
---

RESTful Requests
================

As a review, the four types of HTTP methods called by REST requests are

-   **GET** - retrieve a resource
-   **PUT** - replace a resource
-   **POST** - create a new resource
-   **DELETE** - delete a resource

Mobile REST Objects
===================

Android supports a variety of [Apache HttpComponents](http://hc.apache.org/) that allow for the creation of different REST requests. The basic structure for all the requests it to create an **HTTPClient** object, an appropriate **URI**

    // Create HTTP client
    HttpClient client = new DefaultHttpClient();

    // Create URI
    URI uri;
    uri = URIUtils.createURI("http", host , -1, service, 
                URLEncodedUtils.format(params, "UTF-8"), null);

Once the client and **URI** are created, an appropriate REST object can be instantiated along with an **HTTPEntity** payload if necessary.

HTTPGet
-------

The [**HTTPGet** class](http://developer.android.com/reference/org/apache/http/client/methods/HttpGet.html) produces **GET** requests with optional payload

    // Create HTTP GET request from URI
    HttpGet request = new HttpGet(uri);

    // TODO: Create reqEntity payload object for request as needed  

    // Set payload in request
    // request.setEntity(reqEntity);

    // Execute request and get response
    HttpResponse response;
    response = client.execute(request);

    // Get payload from response
    HttpEntity respEntity = response.getEntity();

    // TODO: Process response payload as appropriate

HTTPPut
-------

The [**HTTPPut** class](http://developer.android.com/reference/org/apache/http/client/methods/HttpPut.html) produces **PUT** requests with optional payload

    // Create HTTP PUT request from URI
    HttpPut request = new HttpPut(uri);

    // TODO: Create reqEntity payload object for request as needed  

    // Set payload in request
    // request.setEntity(reqEntity);

    // Execute request and get response
    HttpResponse response;
    response = client.execute(request);

    // Get payload from response
    HttpEntity respEntity = response.getEntity();

    // TODO: Process response payload as appropriate

HTTPPost
--------

The [**HTTPPost** class](http://developer.android.com/reference/org/apache/http/client/methods/HttpPost.html) produces **POST** requests with optional payload

    // Create HTTP POST request from URI
    HttpPost request = new HttpPost(uri);

    // TODO: Create reqEntity payload object for request as needed  

    // Set payload in request
    // request.setEntity(reqEntity);

    // Execute request and get response
    HttpResponse response;
    response = client.execute(request);

    // Get payload from response
    HttpEntity respEntity = response.getEntity();

    // TODO: Process response payload as appropriate

HTTPDelete
----------

The [**HTTPDelete** class](http://developer.android.com/reference/org/apache/http/client/methods/HttpDelete.html) produces **DELETE** requests (note no payload can be sent with a **DELETE** request)

    // Create HTTP DELETE request from URI
    HttpDelete request = new HttpDelete(uri);

    // Execute request and get response
    HttpResponse response;
    response = client.execute(request);

    // Get payload from response
    HttpEntity respEntity = response.getEntity();

    // TODO: Process response payload as appropriate

HttpEntity
----------

The [**HTTPEntity** class](http://developer.android.com/reference/org/apache/http/HttpEntity.html) is used with the **GET**, **PUT**, and **POST** classes to send additional information in the *requests* and *responses*. In particular, the **getContent()** method can be used on an entity object to retrieve the data which can then be converted as needed. For example, if the contents is a input stream,

	// Get payload from response
	HttpEntity respEntity = response.getEntity();
	
	// Process response payload if non-empty
	if (respEntity != null) {
	   // Get stream from entity
	   InputStream s = respEntity.getContent();
	   
	   // Parse stream
	   BufferedReader r = new BufferedReader(new InputStreamReader(s));
	   StringBuider str = new StringBuilder();
	   String line = null;
	   while ((line = reader.readLine()) != null) {
	      str.append(line + "\n");
	   }
	   s.close();
	   String text = str.toString();
	}
	   
Often the **HTTPEntity** object may contain **XML** that we can subsequently parse which we will see later in the course.