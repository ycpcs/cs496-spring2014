---
layout: default
title: "Lecture 4: Basic mobile framework"
---

Android
=======

Android is a mobile operating system based on a Linux kernel (along with several standard applications) designed by Google that utilizes an optimized JVM to run Java programs. It has built-in support for

-   mobile browsers
-   2D and 3D graphics (via OpenGL ES)
-   SQLite databases
-   many common audio/video media formats
-   telephone capabilities
-   a variety of networking options (Bluetooth, WiFi, etc.)
-   access to common mobile device peripherals (e.g. camera, GPS, accelerometers, etc.)

The [Android](http://developer.android.com/index.html) developers site provides extensive documentation regarding the SDK as well as command references, tutorials, Android conference videos, and forums to discuss Android related issues.

Applications
============

Android applications are written in Java and are compiled into a special package (with a *.apk*) extension which is loaded onto the device. Each application is *sandboxed* such that it runs in its own process and only has access to resources included in the *.apk* file or made available by the system (such as contacts).

Android applications utilize four types of components depending on the desired functionality and lifetime of the component:

-   **Activity** - an activity encompases a single view with a user interface. Typically each different view will correspond to separate activities which the application will seamlessly switch between throughout the use of the application. They should be relatively lightweight to maintain responsiveness of the UI and thus are used to display information to the user and/or collect UI events to pass to other components.
-   **Service** - a service is a more heavyweight component responsible for performing more substantial computations. Typically they do not have a UI (but are utilized by *Activities*) and thus run in a background thread. Services may continue to run even when the application does not currently have focus to perform tasks such as updating data over the network.
-   **Content Provider** - content providers manage shared data between applications. The content provider can allow read/write priviledges to whatever way the persistent data is maintained, e.g. SQLite database, native files, web servers, etc. They provide a unified interface for an application to obtain data independent of the underlying storage mechanism. Typically they also do not have a UI.
-   **Broadcast Receiver** - broadcast receivers are components that are used to listen (and/or respond to via a service) system-wide broadcast announcements (e.g. low battery). Applications can also generate announcements (known as an *Intent*) that other programs may respond to, e.g. a picture has been taken. Again they do not usually have a UI although they may indicate an alert via the status bar.

Applications can expose any of their components for other applications to utilize, e.g. the camera app on the phone allows other applications to start its activity to capture pictures. To activate another application's components (since each application is sandboxed), your application generates a message known as an *Intent* which specifies the type of component you wish to use and the desired action to perform. Then *any* application that is designed to handle the *Intent* is able to process the request and possibly respond to the original application via another *Intent* object.

In order to tell the Android system which components an application will use, an *XML* file known as the *manifest* (in the *AndroidManifest.xml*) is created. It specifies:

-   the names of the components in the application
-   any permissions the application needs (e.g. Internet)
-   the minimum API level necessary for the application to run
-   any hardware devices required by the application
-   any additional libraries the application needs (e.g. Google Maps)

For example, a basic application manifest might be

    <?xml version="1.0" encoding="utf-8"?>
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="edu.ycp.cs.helloandroid"
        android:versionCode="1"
        android:versionName="1.0" >

        <uses-sdk android:minSdkVersion="10" />

        <application
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name" >
            <activity
                android:name=".HelloAndroid"
                android:label="@string/app_name" >
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />

                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>
        </application>

    </manifest>

Lifecycle
=========

An important consideration in mobile applications is the *lifecycle* of the application. Since often times an application will be unloaded at any time by the user, e.g. to take a phone call, or by the system, e.g. due to low memory, it is important to consider how to suspend, destroy, and restore the current state of applications. The simplest way is to simply let the application get destroyed in which case the application will start fresh each time. However this approach can be annoying to users when whatever information they may have entered or were viewing are lost each time they select the application.

A better way is to use methods provided by the component classes to save the current state in some type of persistent manner that can be reloaded when the application is subsequently used. There are a variety of ways Android applications can store persistent data, e.g. SQLite databases, native files, etc., and which one to use is dependent on the information that needs to be stored by the application. Later in the course we will discuss how to handle the volitility of the application state when we investigate creating good UI's.
