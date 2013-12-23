---
layout: page
title: DeploymentBuildWithNewXCode
---



I'm using a project that I have written and compiled with XCode 1.5.  Worked fine then, in both development and deployment builds.
Now, using XCode 2.0, when I try to set the build style to "deployment" it snaps back to "development".  If I set it, close the window, then open it again I find that it has switched back to development.

Is there something else I need to do in XCode 2.0 now?

----

Just upgraded to Xcode 2.0 and Tiger and I have a problem. With all my projects (created under old versions of Xcode), they seem to be stuck on the Development build style. I open a project in Xcode, then double-click on the project name under Groups and Styles to get the project info, then go to Styles. Build style always says Development. If I change it to Deployment and then close the window and then re-open it, it's back to Development. Already tried cleaning the target to see if that would help and it didn't. Already have repaired permissions on the drive.

----

see BuildConfigurations

----

Your project window has a toolbar. In this toolbar is a popup menu that sets the active build style. You may need to customize the toolbar to make it appear.

----
Of course � customize the toolbar!
Got it.  It works now.  Is it some sort of bug, though, that the other way doesn't work?

----
Just some historical perspective: the project info panel is  simply used to modify settings at the level of the project, not to be used for setting the current build configuration of the whole project. It used to be the way to do it, but IMO did not make much sense. The problem is everybody got used to do it this way. The new way of doing it makes more sense... if you have the build configuration popup in the Toolbar!

----
Can someone explain the reason behind the whole "configurations" concept? It just doesn't seem like a good idea to me. What is wrong with having a "debug" and a "release" build? It seems more straightforward. I would be happy to use configurations if anyone could give me a good reason to. Just because "Apple says so" isn't quite sufficient.
----

Configurations allow you to have more than just two types of builds. You can have "Debug", "Release", "Release Universal", etc. Configurations, using .xcconfig files, also allow you to have multiple projects sharing the same settings. Then if you decide that you want all of your projects to, say, use -Os instead of -O3 for their optimization flags, you can change that in one single place and have them all update.

----
I can always have more than two builds anyway. My "Debug" is a standard debug, my "Release" is a 10.2-compatible PPC release, my "Universal" is an Intel-only release. Plus, I can have aggregate targets to build them all at once.

I did not know about the .xcconfig files. Still, it seems like a significant effort and investment into the configuration concept. I don't think it is worth it. I switch between release and debug far more often than I change compiler settings. I have my settings the way I like them and have built my own templates for future use.

I appreciate the information, but I'm still not sold. 
----

In what way does the "configurations" concept make it *harder* to switch between release and debug?

