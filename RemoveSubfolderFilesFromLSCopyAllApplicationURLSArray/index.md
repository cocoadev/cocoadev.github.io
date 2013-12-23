---
layout: page
title: RemoveSubfolderFilesFromLSCopyAllApplicationURLSArray
---


Sorry for the complicated name, but it was the closest i could come.

I got an array with All Application URLs (_LSCopyAllApplicationURLs). The problem is that the method includes files in subfolders like application.app/application.app, or preferencePane.prefPane/application.app and so on. How would i do to remove such items from this array?

----

Maybe check if any of the item's ancestor directories is a bundle, and if one is, remove it.

