---
layout: page
title: NSWindowFocusLock
---



Hi,

I'm trying to create a shape drawing program. I use a NSWindow as the main canvas area and a floating NSPanel as the toolbar palette (to select the shape, or the editing mode, etc.). It works in that I can select the mode by pushing on a button on the palette but that very action takes the focus off the NSWindow and in the end I have to click on the NSWindow twice. Once to make it active and then a second time to start drawing (dragging) the shape.

Any suggestions on how to return the focus back to the NSWindow so that the user doesn't have to click twice? Can I set the NSPanel to respond to button clicks without taking away the focus?  I've seen this behaviour work in Illustrator and Adobe but I'm not sure how it's done.

Thanks
----
Use - (void)setBecomesKeyOnlyIfNeeded:(BOOL)becomesKeyOnlyIfNeeded
documented at <http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPanel_Class/Reference/Reference.html>

----
Thank you that worked fine. I apologize for not knowing. I had been searching for a way to "set" it in the interface builder and I should have made that more clear. The programmatical approach works as well.

Thanks again.

