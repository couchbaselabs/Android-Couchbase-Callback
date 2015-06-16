## Android Couchbase Callback

[![Join the chat at https://gitter.im/couchbaselabs/Android-Couchbase-Callback](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/couchbaselabs/Android-Couchbase-Callback?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

This application provides the fastest way to deploy a <a href="http://couchapp.org/">CouchApp</a> to an Android device using <a href="http://couchbase.org/">Couchbase Mobile</a> and <a href="http://incubator.apache.org/projects/callback.html">Apache Callback (formerly PhoneGap)</a>.

## Requirements

This project requires the latest version of the Android SDK. If you already have the SDK tools, you can upgrade by running `android update sdk`, if you don't have them, you can [install via this link](http://developer.android.com/sdk/installing.html)

## Getting Started

These instructions are divided into two sections, the first describes the development mode.  In this mode you can continually couchapp push your changes in for test.  The second describes distribution mode where you package your application for distribution.

### Development

1.  Clone this repository
2.  Create a local.properties pointing to your Android SDK

    sdk.dir=...

3.  Build this application, either using eclipse or command line tools

    ant debug

4.  Install/Launch this application on your device/emulator

    adb install bin/AndroidCouchbaseCallback-debug.apk

    adb shell am start -n com.couchbase.callback/.AndroidCouchbaseCallback

5.  Couchbase Mobile is now running, you should see now see instructions on screen install your CouchApp.

6.  Forward the Couchbase Mobile from the device to your development machine (the Couchbase port is dynamic and is shown on the screen)

    adb forward tcp:8984 tcp:&lt;value displayed on your screen&gt;

7.  From within your CouchApp project directory, run the following command to install your couchapp on the device.

    couchapp push . http://localhost:8984/couchapp

8.  Refresh the list of CouchApps and navigate to your applicaiton

### Distribution

1.  Compact your database

    curl -X POST -H "Content-Type: application/json"  http://localhost:8984/couchapp/_compact

2.  Copy the database off the device and into this Android application's assets directory:

    adb pull /mnt/sdcard/Android/data/com.couchbase.callback/db/couchapp.couch assets

3.  Repackage your application with the database file included

    ant debug

4.  Reinstall the application to launch the CouchApp

    adb uninstall com.couchbase.callback

    adb install bin/AndroidCouchbaseCallback-debug.apk

    adb shell am start -n com.couchbase.callback/.AndroidCouchbaseCallback

## Examples

Android Couchbase Callback now includes a couple of sample applications to help you get started.

* PhoneGapCouchApp - this is basic PhoneGap demonstration application hosted inside a couchapp
    1. Follow the development instructions above
    2. cd examples/PhoneGapCouchApp/couchapp
    3. couchapp push http://localhost:8984/phonegapcouchapp
    4. Click refresh link on welcome page

* PhotoShare - this photo sharing application shows the power of using Couchbase Mobile with Apache Callback to build real applications
    1. Follow the development instructions above
    2. cd examples/PhotoShare/couchapp
    3. couchapp push http://localhost:8984/photoshare
    4. (Optional) Refactor the ExampleAppActivity class and package name (see https://github.com/couchbaselabs/Android-Couchbase-Callback/blob/master/examples/PhotoShare/src/com/docomoinnovations/couchbase/photoshare/PhotoShare.java)
    5. (Optional) Replace the application icon with a custom icon (see https://github.com/couchbaselabs/Android-Couchbase-Callback/blob/master/examples/PhotoShare/res/drawable/icon.png)
    6. (Optional) Update the app_name string (see https://github.com/couchbaselabs/Android-Couchbase-Callback/blob/master/examples/PhotoShare/res/values/strings.xml)

## Assumptions

A few assumptions are currently made to reduce the number of options that must be configured to get started.  Currently these can only be changed by modifying the code.

-  The name of the database can be anything (couchapp is used in the examples above).  BUT, the design document must have the same name.
    
## Further Customizations

*  Change the name and package of your application

    Refactor name and package of ExampleAppActivity to suit your needs

*  Provide your own custom splash screen

    Override the getSplashScreenDrawable() method to point to your splash screen image

## Frequently Asked Questions

Q: When I start my application the splash screen shows for a long time, then I get the message "Application Error: The connection to the server was unsuccessful."  In the background behind this message I now see my application.  But when I press OK, the application exits.  What is going on?

A: Most likely your application is loading a resource (something like the _chagnes feed) and this causes the PhoneGap container to fail to recognize that the page has loaded.  The fix is simple, add a function to your application that listens for the "deviceReady" event and start your work after this event fires.  For example:

    document.addEventListener("deviceready", onDeviceReady, false);
    
    function onDeviceReady() {
        //  start listenting to changes feed here
    }

We are still looking for a better approach to this problem.

## TODO

* spoof a call to `onPageFinished` so that phonegap paints the screen even if a long ajax call happens before onload.

## License

Portions under Apache, Erlang, and other licenses.

The overall package is released under the Apache license, 2.0.

Copyright 2011-2012, Couchbase, Inc.
