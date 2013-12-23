---
layout: page
title: CustomBundleExtension
---



I can't figure out how to properly assign a custom extension to a bundle. I want it to have .myappPlugin extension. Changing Wrapper extension setting in XCode results in a folder, not a bundle. But if I change folder extension in Finder to .bundle or .plugin, then it appears as bundle.
Is there any other steps I'm missing?

----

Did you try the simple expedient of .myapp.plugin ?

----

Use any bundle/wrapper extension you want.  The Finder learns about wrapper extensions by reading the application plists of applications installed in normal places.  You must install your application in one of the places Finder looks in order for finder to treat your application's wrapper extension as an indication that folder contents should be concealed.  You may also have to restart Finder to get it to look again after you install your application.

There is a configuration file somewhere that lists wrapper extensions so that finder doesn't have to read application plists to find out.  I don't know where the file is, but Apple has a technote or document on the subject.

*Thanks guys. Moving myApp to Applications folder and restarting solved the problem.

.myapp.plugin - nice trick, it works too, but I need a custom extension so I can install plugins by double-click*

----
Add a new document type to your app (eg. myAppPlugin) and give it an icon just as you would for saved files etc. Then your plugins will appear as bundles complete with their own spiffy icons. Also you can double-click on them and your app will open.
----

