---
layout: default
title: "Lab 5a: Using Web APIs"
---

# Getting started

Getting started:

Download [CS496\_Lab05\_Geocoding.zip](CS496_Lab05_Geocoding.zip) and [CS496\_Lab05a.zip](CS496_Lab05a.zip) and import them into Eclipse.

Your task is to write a Java program which, given any two US addresses specified as street address and zip code, will print the distance in miles between them.  To do this, you will write Java code to make HTTP requests to a geocoding web service.

## Making HTTP requests in Java

See [Lecture 5](../lectures/lecture05.html) for details about using [Apache HttpComponents](http://hc.apache.org/) to make HTTP requests.

### Query Parameters, GET vs. POST

Many web services use *query parameters* to specify the data associated with a request.  Query parameters are simple name/value pairs, where both names and values are strings.

Both **GET** and **POST** requests can have query parameters, but they are handled in slightly different ways.

A **GET** request encodes its parameters in a query string, which is part of the URI (Uniform Request Identifier) describing the information the client wants from the server.

Query parameters are good for encoding small bits of information, but are not well-suited to larger chunks of information.

A **POST** request can encode query parameters in the body of the request as a *url-encoded form*. **POST** requests are a better approach than **GET** requests if the request will contain large chunks of data.

It is easy to make POST requests with (url-form-encoded) query parameters using HttpComponents: just use an an **HttpPost** object for the request, and set its entity (body) to be a **UrlEncodedFormEntity**:

    List<NameValuePair> params = new ArrayList<NameValuePair>();
    // ...add NameValuePair objects to params...
    HttpPost request = new HttpPost("http://" + host + "/exampleService");
    request.setEntity(new UrlEncodedFormEntity(params));

Note that the URI used to create the request no longer encodes the value of the **num** parameter.

## JSON

JSON is [JavaScript Object Notation](http://en.wikipedia.org/wiki/Json).  It is a general-purpose format for describing semi-structured data, and is very useful for serializing and deserializing objects.  Many web services use JSON to communicate data to and from the web service.  The result data sent by the geocoding service you will use in this lab is in JSON format.

A JSON document typically will contain a single JSON *value*.  A JSON value can be

* a string
* a number
* an *object*, which is a map of names (strings) to values
* an *array*, which is a sequence of zero or more values

Objects are specified as a comma-separated list of of field names to values within a pair of curly braces (<code>{}</code>).

Arrays are specified as a comma-separated list of values within a pair of square brackets (<code>[]</code>).

You will note that the definition of a JSON value is recursive because objects and arrays can contain nested values.

Example JSON document:

    {
      "postalCodes": [
        {
          "adminName2": "York",
          "adminCode2": "133",
          "adminCode1": "PA",
          "postalCode": "17403",
          "countryCode": "US",
          "lng": -76.712998,
          "placeName": "York",
          "lat": 39.94943,
          "adminName1": "Pennsylvania"
        }
      ]
    }

This document is the result of a geocoding query on an address (725 Grantley Rd, zip code 17403). Note that it specifies a latitude and longitude for the address. Geocoding is useful for applications that need to do operations based on geographic locations.

## Interpreting JSON in a program

Both clients and providers of web services will need a way of converting data to and from JSON format.

As we saw in [Lab 2](lab02.html), the [Jackson](https://github.com/FasterXML/jackson) library and its **ObjectMapper** class makes it extremely simple to convert between Java objects (POJOs) and JSON.

In the **CS496\_Lab05\_Geocoding** package is a class called **PostalCode**, which represents a single geocoding result, and **PostalCodes**, which represents a collection of geocoding results.  The latter (**PostalCodes**) is the type of result returned by the geocoding service.  Let's say that **entity** is an **HttpEntity** whose body contains a geocoding result in JSON format.  The following code will convert it to a **PostalCodes** object:

    PostalCodes p = JSON.getObjectMapper().readValue(entity.getContent(), PostalCodes.class);

As we have seen previously, the Jackson library automatically handles the conversion from JSON to a Java object (POJO).

# Your Task

Your task is to implement the **geocode** method in the **Main** class.  It takes a street address and zip code, and returns a **PostalCode** object representing the result of geocoding the address.

A **main** method is provided.  It takes two street addresses and zip codes, and uses the **geocode** method on them, and then computes the approximate geographical distance between them.  Example run (user input in **bold**):

<pre>
First street address: <b>725 Grantley Rd</b>
First zip code      : <b>17403</b>
Second street address: <b>1600 Pennsylvania Ave</b>
Second zip code      : <b>20500</b>
Distance is 74.80 miles
</pre>

The **ComputeDistance** controller in the **CS496\_Lab05\_Geocoding** project computes approximate geographic distance between two locations specified by GPS coordinates (latitude/longitude).  It uses the following formula:

    double dist = 3956 * 2 * Math.asin(
            Math.sqrt(
                Math.pow(Math.sin((lat1 - lat2)*Math.PI / 180 / 2), 2) +
                Math.cos(lat1 * Math.PI / 180) * Math.cos(lat1 * Math.PI / 180) *
                Math.pow(Math.sin((lng1 - lng2) * Math.PI / 180 / 2), 2)));

## Implementing the geocode method

Use the free geocoding API described here:

> <http://www.geonames.org/export/free-geocoding.html>

You will need to use a **GET** request with the following query parameters:

-   **postalcode** - the zip code
-   **placeName** - the street address
-   **country** - should be set to "US"
-   **username** - should be set to "ycpcs\_cs496"

The result is an JSON-encoded document in the format shown above in the JSON section.  Use the Jackson **ObjectMapper** to convert it to a **PostalCodes** object.  Return the first **PostalCode** contained in that object as the result of the **geocode** method.

<!-- vim:set wrap: ­-->
<!-- vim:set linebreak: -->
<!-- vim:set nolist: -->
