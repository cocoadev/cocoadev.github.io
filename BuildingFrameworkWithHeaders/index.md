---
layout: page
title: BuildingFrameworkWithHeaders
---



I'm trying to build a framework with xCode.

everything works fine except when i'm building, the headers doesnt copy, whay do i have to do?

----

Add a copy files build phase to copy them over.

----

Mm, go down to the 'targets' group, expand your framework target, and select the 'Copy Headers' build phase.  See how there's now a 'Role' column in the main table?  Mark as 'public' any headers you want to be copied over.

----

tnx

the problem now is:


I've added the framework to an application, but when i run the application i get this:


dyld: Library not loaded: /Users/me/Library/Frameworks/myFramework.framework/Versions/A/myFramework
  Referenced from: /Users/me/Desktop/Media/Applications/myApp.app/Contents/MacOS/myApp
  Reason: image not found

when i look up  /Users/me/Library/Frameworks/ it doesnt exists

----

You must build your framework specially if you intend to embed it in an application.  See EmbeddingFrameworksInApplications .

Completely off-topic, but this wiki is a public archive and these pages will become part of the site permanently unless deleted. With that in mind, please try to use proper capitalization and punctuation, make a stab at decent grammar, and avoid shorthand in general.

