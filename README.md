UnidataRadarApp
===============

Unidata Radar App for mobile platforms.

Created by year 2 summer intern Shawn Cheeks.

For an overview of the project, please see:

http://www.unidata.ucar.edu/blogs/developer/en/entry/a_mobile_app_for_radar


==================

The index file is the one that is relevant to the app.  All of the other index_stuff files are pieces of test code that I left because I thought it might be useful to someone down the road.

My application uses Apache Cordova to take a JavaScript/HTML file and package it as a native 
application for mobile devices. In my case, because I have an Android phone, I used the Android 
SDK, but Cordova will also work with other platforms, such as iOS. The Getting Started with Android 
guide can be found at link [1]. One note about the PATH variables is that I also needed to make an 
ANDROID_HOME environment variable (along with /tools and /platform-tools in the path), not just 
ANT_HOME and JAVA_HOME, as described in link [2].

[1] http://docs.phonegap.com/en/2.2.0/guide_getting-started_android_index.md.html
[2] http://spring.io/guides/gs/android/

A basic Hello, World can be created by using the Google Maps JavaScript API v3, following the 
instructions found at link [3]. In order to run the code in a mobile environment, an emulator must be 
set up [4] or a phone must be plugged into the computer [5].

[3] https://developers.google.com/maps/documentation/javascript/tutorial
[4] http://developer.android.com/tools/devices/emulator.html
[5] http://developer.android.com/tools/device.html

The next step is including the Dropbox Datastore API [6]. You need to have a developer account set up 
with Dropbox in order to access the App Console, which gives you the key to put in your program. If 
anyone wants to follow up with my exact program, I can provide access to my Dropbox dev account or 
transfer the application to a Unidata-wide account. Also, a “Hello, World” for the Cordova driver can be 
found at link [7]. Also, in the Dropbox API Redirect URIs (in the App Console), https://www.dropbox.com/
1/oauth2/redirect_receiver must be included. Note that the InAppBrowser [8] must be implemented in 
the Cordova application in order for Dropbox authentication to work. Also note that if using NetBeans 
as an IDE, the all-important InAppBrowser will not work if the app is launched from within NetBeans – it 
must be launched from command line.

[6] https://www.dropbox.com/developers/datastore
[7] https://www.dropbox.com/developers/blog/95/using-dropbox-datastores-in-a-cordova-app
[8] http://cordova.apache.org/docs/en/3.0.0/cordova_inappbrowser_inappbrowser.md.html

Next, the app parses through THREDDs catalog code [9] to find the most recent date, and from that, 
the most recent time. It also accesses the bounding box information from the Google Maps currently 
displayed on the user’s phone. It then makes a request to THREDDs for a KMZ (sample link can be found 
by selecting a date in [9] -> pick a file -> scroll down, Godiva2 -> RADAR-MOSIAC -> 1km National Base 
Reflectivity -> zoom/move to desired location -> Open in Google Earth).
NOTE: This approach was taken because drawing polygons on the Google Map to represent radar 
pixels was too intensive for my phone (or the computer, using Chrome) to handle. The pre-tiled radar 
composites keeps everything much simpler than drawing individual pixels.

[9] http://thredds.ucar.edu/thredds/catalog/nexrad/composite/gini/n0r/1km/catalog.html

In the code, the request is handled by an XmlHttpRequest (XHR) object, and the results from this 
request is stored as a blob datatype. That blob is then written to the user’s Dropbox using the unique 
filename of time + boundingbox. This ensures that when the file is displayed, it does not have to deal 
with a cache file of the same name (before this, I was using a generic.kmz filename, and even though the 
old file had been deleted and replaced by the new one, Maps still displayed the old file). Dropbox then 
creates a download link url, which is then used to create a KMLLayer (note that the url will not work on 
google.com/maps, but does work here).

I then attempted to create an animated KMZ using files from [10], but the KMZ produced is not an 
animation, but a series of images tied to a particular timestamp. This timestamp can be toggled in 
Google Earth, but I could not figure out how to do so in Google Maps. So the alternative was to make 
multiple requests for single-image KMZs and store them in multiple KMLLayers, which are then toggled 
on and off. This is done in a very brute-force manner and is very slow.

[10] http://thredds.ucar.edu/thredds/catalog/grib/nexrad/composite/unidata/catalog.html

Future places this app could expand into are an increase in speed (perhaps the single “animated” KMZ 
would be faster if it will work), other radar products (currently only handles base reflectivity – the 
animated KMZs only produce base reflectivity, other products exist for the single image KMZs), and 
model data (perhaps the HRRR). Another annoying feature that would be nice to manage is the color 
bar legend, as it takes up a large piece of the screen, and the Google Map zooms out when a KMLLayer 
loads to accommodate both the radar image and the legend.
