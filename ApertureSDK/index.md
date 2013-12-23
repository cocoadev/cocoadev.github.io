---
layout: page
title: ApertureSDK
---

Aperture SDK lets you write your customized plugin's and it also allows you to export images to a remote or a local destination.
You cannot write an application or your own action apart from exporting your images.

What does this SDK do?
----
What are the things that can be done with this SDK? Can we develop application using this SDK?

----

And the survey says:

http://developer.apple.com/documentation/AppleApplications/Conceptual/AppleApp_Aperture_001/Overview/chapter_1_section_1.html

----
Yes..i had read the the documentation. but i wanted to know wether we can develop applications using this SDK or it is just a export plugin for Aperture?
If any one finds open source plugin, As well as other plugins developed  using this SDK, then please put it here. apple has already listed some plugins in their site

----
It's an export plugin SDK.  Nothing more.  You certainly can't stand alone apps using the SDK (that's what Cocoa and Carbon are for).  You can't even provide new adjustment tools via it.  Hopefully 2.0 will bring a more expansive SDK, although I can't envisage a SDK that would allow developing new apps ever
----
using this sdk methods Can we get the refference of the master or versioned image? that is.. if supose i want to display the image in a view on that export window how will i do it? 
is it possible to do so? i want to use this sdk to write my own printing options, but i could not get the image refference. 
----
Finally i got image to display in view. Image was in a property dictionry. But if you edit this image it will not modify the image whixh was edited in aperture!
----
That's because you are not editing the image that is in Aperture.  Aperture does not alter the images files.  It stores the data about the alterations and applies them to the master image on demand.  You are not meant to be editing images using the SDK.  It's for export only.
----
How to debug the plugins? i am not finding any other way than NSLog to debug it. buil and run is desabled in xcode..
is it possible to do so? i want to use this sdk to write my own printing options, but i could not get the image refference. 
----
I have created a plugin for http://www.digiproofs.com/ and found debugging a pain at time (the plugin is not release to public yet still testing).   
ps. Apple DTS said they are award of the issue with debugging Aperture plugins and are looking into improving the options.
----
Follow these steps, then you can debug the plug-in. keep the plug-in in development mode and set breakpoint some where. then in xcode goto project->New Custom Executable (i think last option)-> then browse and set the host application( Aperture.app here). Now you can see your 'Build and Debug' options are enabled'. and if you click on that it will launch Aperture automatically and then select your plug-in.                                              --nas
----
I have been blogging my writing of an Aperture export plug-in. Any beginners who want to see what it takes will probably be interested. It's called Random Wok and its purpose is to name the exported images randomly:
http://www.bagelturf.com/cocoa/rwok/rwok.html

