---
layout: page
title: NSCursorWithPlusSign
---




I was under the impression that the nice plus sign appearing below the pointer, while doing a copy thru drag and drop, would be available for Cocoa applications in Panther, but it doesn't seem to be there...

I have however noticed, that a lot more apps now use this pointer, but when dragging files to my application, where I return Copy as the drag operation, the sign does not appear -- what is wrong?

----

Try SetThemeCursor(kThemeCopyArrowCursor). It works fine for me in Jaguar. You could do this in something like the NSDraggingDestination methods or the view's delegate (if it has one).

----

Thanks, it works! I wonder why this 'theme' is not available for NSCursor? OTOH NSCursor's disappearingItemCursor seems to be lacking from the themes...

----

It's there, under kThemePoofCursor. Take a look at http://developer.apple.com/documentation/UserExperience/Conceptual/OSXHIGuidelines/XHIGCursors/chapter_14_section_2.html. The reason for the word theme is that there is an old QuickDraw method, SetCursor, which works with nasty little B&W data structures. The whole "theme" thing started in System 8 with the Appearance Manager, if I remember correctly. As a sidenote, there's also SetCCursor, but let's not go down that path. I just wish they'd add proper support for all the Carbon cursors. I might go hunting in RuntimeBrowser to see if I can subclass anything....

