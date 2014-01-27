---
layout: default
title: "Lab 5: RESTful Android requests"
---

The lab activity is described in the Activity\_ section below.

Background
==========

Android UI Widgets
------------------

Most views in an Android application contain various UI widgets that allow user interaction with the program. The types of widgets and their layout can be described either through XML files (good for static layouts) or created dynamically at run time programatically (good for dynamic layouts). We will explore designing UI layouts later in the course. For this lab, the view layout is contained in the *res/layout/main.xml* file which will be loaded by the activity's **.onCreate()** method. An example file for a basic application that simply displays a single line of text is:

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical" >

        <TextView
            android:id="@+id/helloLabel"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="@string/hello" />

    </LinearLayout>

Note that the *TextView* (which is basically a label) can be referred to in the application by its *id* (**helloLabel** in this example). The *id* allows us to access and/or modify a UI component. Thus, for example, we could programatically change the text for the label in the above Activity by

    TextView label = (TextView) findViewById(R.id.helloLabel);
    label.SetText("How are you?");

Here the method **.findViewById()** accesses the resources of the application through the *R* object. Note the correspondance between the requested *id* and the one specified in the XML file.

HTTP requests in Android
------------------------

Since Android applications are written in Java, we can utilize many of the same classes as we did on the PC when accessing web services. Thus we can issue requests and receive responses (possibly in XML) and process them in a similar fashion. In particular, we can use the same [Apache HttpComponents](http://hc.apache.org/). discussed in [lab01](lab01.html) (For complete details, [read the tutorial](http://hc.apache.org/httpcomponents-client-ga/tutorial/html/).) Hence an example web request to the geocoding service from [lab01](lab01.html) might be

    // Create HTTP client
    HttpClient client = new DefaultHttpClient();

    // Create list of request paramater/value pairs
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("postalcode", "17403");
    params.add(new BasicNameValuePair("placeName", "725 Grantley Rd");
    params.add(new BasicNameValuePair("country", "US"));

    // Create URI
    URI uri;
    uri = URIUtils.createURI("http", "ws.geonames.org", -1, "/postalCodeSearch", 
                URLEncodedUtils.format(params, "UTF-8"), null);

    // Create HTTP request from uri
    HttpGet request = new HttpGet(uri);

    // Execute request and get response
    HttpResponse response;
    response = client.execute(request);

    // Copy the response body to a string
    HttpEntity entity = response.getEntity();

At this point, the web service has provided an XML response that we need to extract from the response payload and parse accordingly. However, Android does not have native support for **dom4j** but does have an alternative XML parsing library **XPath**.

Parsing XML via XPath
---------------------

To parse an XML message in Android, we will use an alternative library that directly accesses the XML tree through XPath expressions. Thus we simply need to create an appropriate document (through a factory) from the payload and obtain the desired data via an XPath to the corresponding field, e.g.

    XPath xpath = XPathFactory.newInstance().newXPath();
    DocumentBuilderFactory builderFactory = DocumentBuilderFactory.newInstance();
    builderFactory.setNamespaceAware(true);
    DocumentBuilder builder = builderFactory.newDocumentBuilder();
    Document doc = builder.parse(entity.getContent());
    Node latNode = (Node) xpath.evaluate("//geonames/code/lat",doc,XPathConstants.NODE);
    Double lat1 = Double.parseDouble(latNode.getTextContent());

Activity
========

Starting point code: [CS496\_Lab05.zip](CS496_Lab05.zip).

Your task is to duplicate the functionality from [lab01](lab01.html) in an Android application. However now instead of obtaining the street addresses and zip codes from the terminal, in the **CallWebService()** method the information should be retreived from the [EditText](http://developer.android.com/reference/android/widget/EditText.html) widgets from the Activity's UI (refer to *res/layout/main.xml* for the corresponding *id*'s of the widgets). Once the HTTP requests are generated and the XML responses are parsed using an XPath object, the result can be computed and displayed in the **distanceLabel** widget using the **.setText()** method. Thus a sample run in the AVD is

> ![image](images/lab03/FirstWebService.png)

As a reminder of the geocoding API:

> <http://www.geonames.org/export/free-geocoding.html>

You will need to use the following parameters:

-   **postalcode** - the zip code
-   **placeName** - the street address
-   **country** - should be set to "US"

The result is an XML-encoded document in the format shown above in the XML section.

You can find the (approximate) distance in miles between two points (*lat1,lng1* and *lat2,lng2*) using the following formula:

    double dist = 3956 * 2 * Math.asin(
            Math.sqrt(
                Math.pow(Math.sin((lat1 - lat2)*Math.PI / 180 / 2), 2) +
                Math.cos(lat1 * Math.PI / 180) * Math.cos(lat1 * Math.PI / 180) *
                Math.pow(Math.sin((lng1 - lng2) * Math.PI / 180 / 2), 2)));
