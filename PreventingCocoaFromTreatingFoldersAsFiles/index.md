---
layout: page
title: PreventingCocoaFromTreatingFoldersAsFiles
---



Hopefully somebody can fill this in.

When I create a Cocoa open dialog to open files and specify an array of "types" (or rather filename extensions), it won't allow me to open files within folders which also have the same extension. Calling setCanChooseDirectories:NO has no effect, it will still try to open the folder instead of view its contents. What the heck?

----

A question worth asking is, why do the folders have the extension in first place? If they have, they are begging to be treated as bundles by Mac OS.
Anyway, there was a method that allowed the panel to "go into bundles" I believe. Maybe this is what you are looking for.

----

The folders are on a CD which wasn't specifically mastered for finicky OS X Cocoa applications.

