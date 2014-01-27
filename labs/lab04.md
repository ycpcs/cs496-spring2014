---
layout: default
title: "Lab 4: Hello, Android!"
---

Background
==========

Android ADT bundle (ADT)
------------------------

Since Android applications are written in Java, any standard Java IDE can be used or development. However, Google provides plug-ins for Eclipse as well as a preconfigured IDE [Android Developer Tools (ADT)](http://developer.android.com/sdk/index.html). There is also an early release development environment called [Android Studio](http://developer.android.com/sdk/installing/studio.html) based on the IntelliJ IDEA development environment. For this class, we will be using the Eclipse ADT bundle.

Android Virtual Devices (AVD)
-----------------------------

In order to test our Android applications (since we do not have actual Android devices) we will need to create an *Android Virtual Device* (AVD). This emulator will then be invoked and the corresponding *.apk* file downloaded to the emulator when we run our application. To create an AVD (the given example values are the ones we will use in this class):

-   In Eclipse select *Window -\> Android Virtual Device Manager*
-   Select a *New* AVD from the top button on the right side
-   Give the AVD a meaningful name, e.g. **Test\_AVD**
-   From the *Target* dropdown box, select the desired API level the emulator should use, e.g. **Android 2.3.3 - API Level 10**
-   In the *SD Card* box, make a small sized virtual SD Card, e.g. **10** MiB
-   In the *Skin* box, choose a built-in device resolution, e.g. **WQVGA432**
-   Select *Create AVD*

You can create as many AVD's as you wish to test your application on a range of different API's and device resolutions.

Android Application File Structure
----------------------------------

Android applications use a similar directory structure as other Java applications:

-   *src* - where component source files are located
-   *assets* - where any files used by the application can be placed, e.g. database files
-   *res* - resource file directories

    > -   *drawable-*\* for various image files (e.g. program and button icons). The different *\*dpi* subdirectories allow for different icons to be used depending on the resolution of the device the application is being run on.
    > -   *layout* - contains the XML layout files for each Activity used in the application, e.g. **main.xml** for a single Activity application
    > -   *values* - contains XML files for any static values the application uses, e.g. **strings.xml** for static string constants.

The other directories contain auto-generated files for the application. Additionally, the **AndroidManifest.xml** file contains a list of all components the application will use along with permissions for services, etc.

Activity
========

-   Obtain the ADT bundle zip file from the instructor and extract it onto the USB flash drive.
-   Inside the extracted directory should be an **eclipse** subdirectory with the **Eclipse** executable. Launch Eclipse from this subdirectory and place the workspace in a convenient folder on your **H:** drive, e.g. **H:\My Documents\android**.

Starting point code: [CS496\_Lab03.zip](CS496_Lab03.zip).

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
