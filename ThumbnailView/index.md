---
layout: page
title: ThumbnailView
---

Creating a thumbnail view for a bunch of images seems to be a very common task, yet I can't find any code examples. Does anyone have any? 

-- Erik

----

General/NSMatrix, General/NSImageCell --zootbobbalu

----

Yeah, that about as much information as I've found. But there's a lot more than that to it.. Something like a class that takes a General/NSArray with image filenames as input, is capable of handling lots of images efficiently (fast thumbnail loading and scrolling), has resizable, selectable, sortable and clickable thumbs and holds a General/NSArray of selcted images filenames would be really nice...  -- Erik

----

The General/IPhotoLikeView page is a great place to start. Among other options, it's not terribly difficult to draw multiple images in a single large General/NSView subclass using things like -drawAtPoint:fromRect:operation:fraction . -- General/PaulCollins

----

Apple's Cocoa Performance Guidelines document is also useful when you're designing things like this.

http://developer.apple.com/documentation/Cocoa/Conceptual/General/CocoaPerformance/index.html

- General/MarcCharbonneau

----

Look at General/MUPhotoView -- General/BjoernKriews
