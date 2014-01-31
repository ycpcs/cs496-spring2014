---
layout: default
title: "Lab 5a: Using Web APIs"
---

The lab activity is described in the Activity section below.

Background
==========

Web APIs
--------

Web APIs are the simplest form of web services. Although there is no standard for Web APIs, they ususally:

-   send request data as query parameters (HTTP GET) or form parameters (HTTP POST)
-   return response data in the body of an HTTP response, encoded as XML or JSON (although it could be encoded in any format)

You can think of a web service as being, essentially, a procedure. You provide it some input parameters, it does some computation, and then returns a result.

### Making HTTP requests in Java

Most flexible technique: use [Apache HttpComponents](http://hc.apache.org/). For complete details, [read the tutorial](http://hc.apache.org/httpcomponents-client-ga/tutorial/html/).

Example: let's say that we have a web service available that determines whether or not an integer is a prime number. It is accessed using the URI **/isPrime** on the server. It takes a single parameter, called **num**, which specifies the integer to test. It returns a plain text response consisting of the text **true** or **false**. If the request was not specified properly, such as not specifying a **num** parameter, or specifying a **num** parameter that is not an integer, the service returns a response with the code 400 (Bad Request), and returns a plain text error message in the body of the message.

Here is code to use this web service:

	Scanner keyboard = new Scanner(System.in);
	
	// Get the host (server) the web service is running on
	System.out.print("Host for web service: ");
	String host = keyboard.nextLine();
	
	// Get integer to check
	System.out.println("Integer to check: ");
	int num = keyboard.nextInt();
	
	// Create an HttpClient
	HttpClient client = new DefaultHttpClient();
	
	// Create a URI to access the web service, encoding the
	// number as a query parameter
	List<NameValuePair> params = new ArrayList<NameValuePair>();
	params.add(new BasicNameValuePair("num", String.valueOf(num)));
	URI uri = URIUtils.createURI("http", host, -1, "/isPrime", 
		    URLEncodedUtils.format(params, "UTF-8"), null);

	// Create an HttpGet object, which is the HTTP request
	// we want to send
	HttpGet request = new HttpGet(uri);

	// Execute the request, getting back an HttpResponse object
	// representing the server's response
	HttpResponse response = client.execute(request);

	// Copy the response body to a string
	HttpEntity entity = response.getEntity();
	InputStreamReader reader = new InputStreamReader(entity.getContent(), Charset.forName("UTF-8"));
	StringWriter sw = new StringWriter();
	IOUtils.copy(reader, sw);
	String responseBody = sw.toString();
	
	// The response's status code will indicate whether or not
	// the request succeeded
	if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
		System.out.println("Error: " + responseBody);
	} else {
		System.out.println(responseBody);
	}

### GET vs. POST

An HTTP GET request encodes its parameters in a query string, which is part of the URI (Uniform Request Identifier) describing the information the client wants from the server.

Query parameters are good for encoding small bits of information, but are not well-suited to larger chunks of information. An HTTP POST request encodes query parameters in the body of the HTTP request.

It is fairly easy to make POST requests using HttpComponents: just use an an **HttpPost** object for the request, and set its entity (body) to be a **UrlEncodedFormEntity**:

    HttpPost request = new HttpPost("http://" + host + "/isPrime");
    request.setEntity(new UrlEncodedFormEntity(params));

Note that the URI used to create the request no longer encodes the value of the **num** parameter.

JSON
----

JSON is [JavaScript Object Notation](http://en.wikipedia.org/wiki/Json).  It is a general-purpose format for describing semi-structured data, and is very useful for serializing and deserializing objects.

An XML document consists of elements specified by a *start tag* and an *end tag*. Between the element's start and end tag are any number of child elements, interspersed with any number of chunks of text.

Example JSON document:

    TODO: put JSON result of geocoding here

This document is the result of a geocoding query on an address (1600 Pennsylvania Ave, zip code 20500). Note that it specifies a latitude and longitude for the address. Geocoding is useful for applications that need to do operations based on geographic locations.

Interpreting JSON in a program
------------------------------

Both clients and providers of web services will need a way of converting data to and from JSON format.

As we saw in [Lab 2](lab02.html), the [Jackson](https://github.com/FasterXML/jackson) library and its **ObjectMapper** class makes it extremely simple to convert between Java objects (POJOs) and JSON.

TODO: code examples

Activity
========

Getting started:

Download [CS496\_Lab05\_Geocoding.zip](CS496_Geocoding.zip) and [CS496\_Lab05a.zip](CS496_Lab05a.zip) and import them into Eclipse.

Your task is to write a Java program which, given any two US addresses specified as street address and zip code, will print the distance in miles between them.

Example run (user input in **bold**):

<pre>
First street address: <b>725 Grantley Rd</b>
First zip code      : <b>17403</b>
Second street address: <b>1600 Pennsylvania Ave</b>
Second zip code      : <b>20500</b>
Distance is 74.80 miles
</pre>

Use the free geocoding API described here:

> <http://www.geonames.org/export/free-geocoding.html>

You will need to use the following parameters:

-   **postalcode** - the zip code
-   **placeName** - the street address
-   **country** - should be set to "US"

The result is an JSON-encoded document in the format shown above in the JSON section.

You can find the (approximate) distance in miles between two points (*lat1,lng1* and *lat2,lng2*) using the following formula:

    double dist = 3956 * 2 * Math.asin(
            Math.sqrt(
                Math.pow(Math.sin((lat1 - lat2)*Math.PI / 180 / 2), 2) +
                Math.cos(lat1 * Math.PI / 180) * Math.cos(lat1 * Math.PI / 180) *
                Math.pow(Math.sin((lng1 - lng2) * Math.PI / 180 / 2), 2)));
