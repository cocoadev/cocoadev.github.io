---
layout: page
title: ExportToiPod
---



Anybody have an idea how you can export data to an iPod from a Cocoa app? OmniOutliner can do this, and several other apps I've seen. I'd love to have this feature, at least for future reference. Is there a framework or some other form of easy way to do it? --LoganCollins

----

You mean as a Note, accessible on the iPod via Extras > Notes? They're just plain text files. Use NSString's     writeToFile:atomically: method.

----

But how do you send it to the iPod? Is there a special volume where it saves to, or what? Also, how can you tell if the user has an iPod connected or not? --LoganCollins

*Search this site!! ListOfConnectediPods gives you the list of connected iPods. An iPod will show up in the /Volumes directory if it's in disk mode, which is enabled in iTunes. Use the code at the bottom of that page - it's the most optimized and efficient.*

