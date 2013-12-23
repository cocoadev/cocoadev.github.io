---
layout: page
title: DifferenceBetweenFrameAndBounds
---

Can someone kindly take the time to explain the difference between the "Frame" and the "Bounds" of an NSView?

I think I understand more about the Frame, but when I start playing with the Bounds of my view strange things happen, and I can't find an easy explanation of the difference and relatedness of these two concepts, the developer documentation is a little too concise for me to extrapolate anything from.

----

The frame is expressed in the coordinate space of a view's superview. The bounds are expressed in a view's own coordinate space.
 
[http://developer.apple.com/documentation/Cocoa/Conceptual/DrawViews/Concepts/ViewHierarchy.html]

That's really all there is to it, but that has some wider implications than you might initially think. For instance, if you have a view with a frame width of 200, and increase the bounds width to 400, everything will be drawn half as wide as it was before (because there are now twice as many points occupying the same area of the screen).

*In a similar way the origin of the bounds can also be changed so that e.g. {0, 0} lies in the middle of the view.*

----

What do I do if I want everything that's drawn to stay the same size even when I zoom the view? The new bounds will represent some scaling
of the bounds before the view was zoomed. My zooming and scrolling work fine, but I am drawing with NSBezierPath (which - obviously -
get zoomed along with the view) - and explains why new drawing I do while the view is zoomed turns to dust when I unzoom back to 100%.

Should I be looking into bitmaps?

----

Whaoww! I read that 5 times and I don't think I totally understand. But apparently, you are talking about changing the bounds of a NSView where something is already drawn, and you want that something not to change? And then you draw something else? You may have reached the limit of the usefulness of bounds. You might be better off not using the bounds, then, but scale your NSBezierPath appropriately before drawing them. It is quite easy with NSAffineTranform. --CharlesParnot

----

My app has a kind of "mapping" component. I'd like to mimic the functionality of the Preview app, except that instead of displaying a canned image I'll be displaying a map derived from some data. The contents of the map are unimportant, but imagine a big grid; one instance of a map might be 200x100 units, another might be 150x300 units. I'd like to allow users to zoom in/out, scroll around if the zooming level occludes parts of the map (which it almost certainly will), etc. In addition, users will need to be able to click in the map to select cells, and will also be allowed to add/modify map data and rendering options, all of which will require all or part of the map to be redrawn.

I've toyed with subclassing NSView (and sticking it inside an NSScrollView in IB), but the "size" of the NSView becomes an issue; I want to set the bounds of the view to correspond to the dimension of the map (so that the edges of the map are at the edges of the view), but that ends up scaling the view - and I can't preset the view's bounds in IB because I don't know the bounds until runtime.

----

I started working on a similar problem, but with vector data. The problem I couldn't answer was how to handle scaling and origin to real map coordinates. On most of my maps using Albers projection, the x coordinates are in the range of 4,000,000 and y in the range of 380,000. How do you plan to handle map versus screen coordinates? Do you plan to use NSView's transform methods, or manage a transform in your data model prior to display? What do you plan your source to be? GeoTiff, the GDAL library?

I'm leaving on vacation, but will be back Dec 28. When I get back I would be willing to collaborate on a generic MapView that can handle both vector and grid data.

PhilipRiggs
priggs at cnr dot colostate dot edu

----

As it turns out, my map does not represent "real world" data, it's more of a network arranged on a hexagonal grid: think "wargame". As for scaling etc, I was hoping to leave the heavy lifting to NSView.

----

Set the view's frame, not its bounds, and you can get a custom size without accidentally scaling it.

----

Embedding your NSView into a NSClipView is the way to go for scrolling, if you want to keep efficiency when redrawing your view. The NSClipView shows only what the user is supposed to see. Your NSView moves behind your NSClipView as you scroll. So you get : NSView --> Embedded into NSClipView --> Embedded into NSScrollView.

----

I understood the view hierarchy inside an NSScrollView, but had fuddled the notions of frame and bounds. Obviously, I can resize the frame of an NSView to whatever gargantuan dimensions I might please, the bounds will change correspondingly but without hosing the coordinate space, and the enclosing NSScrollView/NSClipView pair will handle clipping and scrolling for me. The next trick is to have the scrollbars update when I scale the NSView ...


----
Sorry if this will look like a RTFM, but it is not meant this way. It is actually just a coincidence that Apple has just published these:

http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaViewsGuide/index.html

http://developer.apple.com/documentation/Cocoa/Conceptual/NSScrollViewGuide/index.html

http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDrawingGuide/index.html

The first has a good intro on Bounds/Frame. The second talks about NSSplitView. The third has some more about affine transforms and coordinates. It is like Apple was trying to answer all your questions after reading your post!

