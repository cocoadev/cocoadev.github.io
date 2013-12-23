---
layout: page
title: DropZoom
---

I want to create a similar effect as used in the finder: when dragging a file onto a folder in the sidebar, the files sort of zooms into it.  This is also done in Address Book, which leads me to believe there is a Cocoa (private?) way of doing this.  Any ideas?

----

I don't know a lot about this stuff, but I would think that you would have to be dropping something from your own app into your own app. Lets say you had some source view and you wanted to drag a NSTableView item into a new source item. If you made your own custom drag image for the tableview, you could do a little animation on mouse up. If you are wanting to do a drop from the Finder to your app, I doubt you could do it very easily without some kind of hack.
--ZacWhite

