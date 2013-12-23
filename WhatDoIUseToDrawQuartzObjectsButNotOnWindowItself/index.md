---
layout: page
title: WhatDoIUseToDrawQuartzObjectsButNotOnWindowItself
---

Hi. I am making a raster graphics editor to compete with Photoshop (with all the features :-) ), and I decided to use the Quartz API for drawing. The thing is, I saw on some documentation that you can draw directly onto a General/NSWindow using Quartz. Instead, I want to have a separate space for drawing. How do I do so? - Pietro Gagliardi http://web.mac.com/pietro10

----
You can draw into an General/NSImage using lockFocus and unlockFocus. You can also create an General/NSGraphicsContext with an General/NSBitmapImageRep as its backing store and draw into that.

----
Thanks. How do I create either of them with Interface Builder?

----
You do not.

----
So how do I programatically add controls to a window and have a controller make a connection to that object?

----

To draw into an General/NSImage:

<http://developer.apple.com/documentation/Cocoa/Conceptual/General/CocoaDrawingGuide/Images/chapter_7_section_5.html#//apple_ref/doc/uid/TP40003290-CH208-DontLinkElementID_52>

You sure you don't just want to draw into an General/NSView?

----
I drag an General/NSView into my window in Interface Builder. I get the blue box with Custom View in the preview. When I test, I see nothing. Do I have to do anything special in order to make this General/NSView visible when a new document is available?

Also, if my image is too big, will General/NSView provide scrollbars?

----

Read: 

http://developer.apple.com/documentation/Cocoa/Conceptual/General/CocoaViewsGuide/index.html

----

" I am making a raster graphics editor to compete with Photoshop (with all the features :-)"... Sounds great. I for one wouldn't want to discourage you. BUT. Your questions suggest that you are not likely to achieve it. This stuff is HARD. It takes YEARS to build this sort of app. If it weren't the case there would be hundreds of Photoshop clones all competing for that market slot, rather than about three (yet with Photoshop itself still top of the heap). I think you have grossly underestimated exactly what Photoshop does, let alone what it takes to implement it. Still, good luck!

----
It's probably time to do some General/CocoaTutorials and buy some General/CocoaBooks. At your current level, "clone Photoshop" is not a good immediate goal, it's too far beyond your capabilities. Start smaller. There's nothing wrong with a lofty goal for the end, but you're going to have to approach it in steps. Get used to the basics of Cocoa, then move on to the advanced bits, then once you've made a few simple apps with all that, you'll be ready to make your Photoshop clone.
