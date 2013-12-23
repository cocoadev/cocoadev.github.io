---
layout: page
title: AccessingApplicationResources
---


This is something that most applications do, but I cannot figure out how to implement it. The program I am working on has templates that I want to be user edit/add able. The convention seems to be storing them in the Application Support folder if they are not there. They are automatically created either programmatically or by copying from the bundle. I need to be able to store a standard templates file in the application bundle, and automatically copy it to Application Support if it is not there. Is there a Cocoa way to make an Application Support folder (Like [NSUserDefaults standardUserDefaults])? 

I have begun making a function that is passed in what you want (e.g. templates, application support folder, plug-ins). It checks if it is there and if not copies it from the bundle. Then it passes the location. Is this the best way to do this? How do I access the files in an application's bundle?

----
In general, I'd say you've got the right approach: check for the existence of an Application Support folder for your app. If it doesn't exist, create it by copying a default set of files from your application's Resources folder inside the app's bundle.
*How do I access the files in an application's bundle?*
Check out NSBundle, especially mainBundle and the various pathForResource methods.

