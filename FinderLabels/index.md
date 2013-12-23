---
layout: page
title: FinderLabels
---

Does anyone know how to get/set the flag for a file's label in the finder? I was hoping it was just a simple attribute in NSFileWrapper, but it's not. Any ideas?
Thanks, 
BrandonDelcamp

----

Have you looked at the AppleScript dictionary for the Finder?

----
Yeah, it says 
Class Label: (NOT AVAILABLE YET) A Finder label (name and color)

BrandonDelcamp

----

You'll probably have to use Carbon for this. [http://developer.apple.com/documentation/Carbon/Reference/IconServices/icon_services/function_group_10.html#//apple_ref/c/func/SetSuiteLabel]

----

If what you're working on is licensed under the GPL, you can use the source of the 'setlabel' utility, part of osxutils, to help you:
http://osxutils.sourceforge.net/

----

Couldn't you stick it in your bundle and call it via NSTask to avoid having to even think about the GPL?

----

Yep.

