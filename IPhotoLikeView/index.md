---
layout: page
title: IPhotoLikeView
---

I am working on a project that will make use of a view similar to iPhoto's main photo-view. Problem is, I haven't done much with views in the past and I don't know what to subclass...
NSView? NSControl? NSScroller?
That's not the only issue I'm having.
Another is how to conceptually draw a view. You see, I could use a for(){} block, and I probably will, but I'm not so sure. And this application needs to be able to change the images (Of course) depending on the document.
I'd appreciate some help.
I've skipped on some of the details of this "App" (there I go again) and if necessary, I'll post more information.
Thanks in advance.

Thanks for the help everyone!
However, I've come up with a wonderful thing:
NSBoxes.
I made an NSBox with the title hidden and no border, so it's invisible, I'll then use a plugin to hand an NSView full of NSImageViews (In a NIB file, specifically. I dragged "customView" to the instances tab and then added NSImageViews into the window that appears  for that.
The inspiration: I figured since NSTabView has dynamic content loading from views, why wouldn't NSBoxes? I was working on something else when I came to that conclusion.
One last thing:
Can anyone suggest how I'd accomplish dragging FROM NSViews? (With messages, bindings, or subclassing, or categorizing or what-have-you)

----

I belive iPhoto uses a custom NSView which is what I suggest you do. You could also try a subclass of NSMatrix or NSTableView, but both of those probably do more than you need and therefore add unnecessary complications. It shouldn't be too hard to make an NSView subclass with an NSMutableArray of NSImages. Override the     drawRect: method and simply draw the images at the appropriate size and location you want. Put that view in an NSScrollView and resize the frame appropriately and you've got it made. Of course implementing selecting and dragging and dropping can be a little trickier, but you can get to that once you've got the basics down.

On second thought, if the view requires user interaction, an NSControl subclass might be the "right" way to go, but iPhoto doesn't do this for some reason or another. I also think it is simpler to not even deal with NSCell, but others might have a more experienced opinion.

-- RyanBates

*Speaking of which, a tutorial on subclassing NSControl and how it interacts with NSCell would be very helpful. Apple's docs just have a few paragraphs on it. Anyone know of one on the web or have enough experience to write one up?*

----

I think this sounds more like a matter of implementing a dynamic layout container. So, to promote my own work, check out FlowLayoutView.

It isn't a NSControl subclass, but my FlowLayoutView will automatically give you iPhoto like flowing/relayout from top/left to bottom/right with resizability. Plus, vertical scrollbars are handled automatically. It's easy enough to just insert your own NSControls or whatever into it -- they can handle the mouse-clicking. In my opinion, it shouldn't be the responsibility of the layout mechanism to track the mouse.

The performance isn't bad at all. I had *hundreds* of views inserted and layout was real-time. The only gotcha is this: make certain the views you insert are fully opaque ( via      - (BOOL) isOpaque { return YES; }  ) -- the reason is that when you have a ton of subviews each one will require the flow layout to redraw itself for their compositing.

--ShamylZakariya


----

I think Having your own view that lays out NSCells instead of NSViews will give you much better performance. Yes you'd have to implement the event handling in your view, but the savings in memory and cycles when you're dealing with thousands of photos should be significant.

----

MUPhotoView is a component that attempts to replicate the the iPhoto view: http://blakeseely.com/blakeseely.com%20-%20Downloads.html

----
Has anyone ever used MUPhotoView with an NSTreeController as the source for the thumbnails? I know it works with NSArrayController, but I wonder if it could support a tree, with drag and drop?

----

Those who have implemented iPhoto-like views for images might like to share tips about how to boost performance. I have a custom view that displays just like iPhoto (I don't use cells nor subviews, I just figure out which bit of the view needs updating and paint the images in directly within some decorative border which is drawn using NSBezierPath - I figure this should give better performance than having separate objects in the "cells" though I haven't tried the alternative to see for sure). Anyway, when I paint an image, the underlying NSImage is initialised lazily from a list of filenames on disk - so in other words, I store a list of objects which contain both the filename, and once it has been displayed, the actual image itself. This approach works fairly well, in that images that are never viewed are never created. Of course the creation of the image on demand slows things down on the first scroll through the view, so there's one performance hit. However, though things speed up once the images have been cached into memory, it means I'm retaining hundreds of NSImage objects, which uses up lots of space. Once this starts getting paged to disk things slow down again. I don't see similar problems with iPhoto so I have to wonder how it's doing it, and with many more images than I'm tending to use. Maybe it would be better to cache the images in one huge offscreen image that the browser displays rather than hundreds of retained NSImage objects which are individually displayed. Overall I would assume this would use the same amount of memory however (if not a little more). Any ideas about how to make this really fly would be good! --GrahamCox

----

What about just compositing the images to an NSView, and using mouse events to simply calculate the item that the user clicked on? If you can composite them on load, then the user won't be too annoyed if the speed is decent when they're scrolling. MUPhotoView has some techniques for speeding up scrolling, mainly by loading the lo-res image, and then getting a higher-res image when scrolling stops. He describes it in the example's ReadMe, I believe.

----

OK, MUPhotoView looks interesting. Basically, its protocol calls for a "fast photo" at certain times (like live resize) and an ordinary one otherwise. This puts the onus on the data provider to implement the speed-up, which is fair enough. Problem as I see it is that what tends to be slow is: a) loading and decoding the JPEG data off disk, and b) rendering a scaled image. MUPhotoView only really helps with (b), by asking its data provider to supply a pre-scaled image. I think that's worthwhile, but it still leaves the question of how best to deal with (a). Of course there's no way around a, but perhaps there's a way to handle it so that the user isn't made so aware of how slow it is. I guess iPhoto deals with (a) by making thumbnail images that it stores on disk along with the "real" image, thus loading and decoding these is much quicker. The thumbnails can be used for scrolling and live resize, with the high-res versions being loaded when the user pauses (and hence isn't aware of how slow each image is loading). Seems like a good way to do it except for the very slow creation of the thumbnails first time around. As an aside it would be nice if you could ask for low-res data from a JPEG when it was loaded rather than having to manage two paired files - maybe this is something that JPEG2000 can do... oh well, it's only 6 years after 2000, I guess waiting a bit longer won't kill me. Or good old resource forks storing a low res preview within a fork of the image file - now whatever happened to THAT idea? --GrahamCox

MUPhotoView has just been updated to version 1.2. Check it out.

----
I love MUPhotoView, but it slows to a crawl when doing drag selections with more than 20 images visible at once.  I'm not sure what the problem actually is, but as best I can discern, it redraws every single visible image at every interval in the drag process, even those not selected.  Add the need to scale every image into that, and ouch.  (Version 1.0 is much, much faster in this regard.  I just downgraded).

----

I think he mentioned in his blog that he fixed performance issues related to drag in the latest (1.2) one. Have you tried that? It's kind of weird that it's faster in the older one. Send him an email about it.

----

Setting the background to <code>[NSColor clearColor]</code> causes all sorts of redrawing artifacts.  Is there any way around this?  My app looks far less "seamless" when I'm forced to use a solid background color, but wiping the view with clearColor doesn't erase any of the shadows of selection drawing.  Any ideas?

----

I would guess that your view doesn't know you're drawing outside of the expressed rectangle where your photo is.  With a color, when you were changing something, like selection, the view was redrawing itself entirely (I bet PercentDraw would show 100%) and so it was erasing those old shadows.  But with clear as the color it wasn't redrawing in that part of the view.  What you need to do is, every time you change selection, tell the view it needs to redraw an area slightly larger than the frame of the picture to include the shadow. ie

    
[MyPhotoView setNeedsDisplayInRect:NSInsetRect([PhotoCell frame], -3, -3)]; //You will need to play with these numbers depending on your shadow

