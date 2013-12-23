---
layout: page
title: DrawingClickableObjectsLikeAppleWorks
---

Hi.  I'm trying to create a seating chart creator.  I'd like to be able to draw the desks and make it so the user can drag it around and enter the names on the desks wherever on the page.  I can draw the desks but I have no idea how to make it clickable and editable like the rectangles are in Appleworks or Pages.  Any help will be appreciated.

Gabe

----

Well you could have a base class, which would have an NSRect contentRect variable (which contains the x, y, width and height). Then each draggable object would interhit from that (so you could have a Chart class, Drawing class, etc) and then each of those separately handle drawing and whatever for their view. But then the main view would have an array of each of these draggable objects (base class), and so then you just handle drag and drop appropriately for each object in the array. Oh, and each object (in your base class) would have a z-index, so you can keep track of which objects are on top. So if the user clicks, you could find the object in your array whose contentRect contains that mouse point (NSPointInRect) and then if there are more then 1 objects, you use the object with the highest z-index. Makes sense??

----

Well I must admit that drawing something in Cocoa is a completely new thing for me could you maybe give me a basic step by step process of how to do this?  I just don't understand exactly what I should do.  Sorry for the dumb questions but I'm a self-thought programmer and I'm still learning.  Also is there a way to draw buttons in a say NSView so it can be dragged around.  It might be easier that way.

----

Well drawing is done several ways, depending on the object you want to draw. If you have an NSImage you want to draw to a view, you would use it's     drawInRect:fromRect:operation:fraction method. If you have an NSString, you would use its     drawInRect:withAttributes: method. If you want to do custom drawing, you would use an NSBezierPath object, which allows for drawing rectangles, ovals, lines, and arcs. All drawing in Cocoa should be done either into an NSImage or into an NSView. Drawing into an NSImage requires you to intialize an NSImage via     initWithSize: and then put your drawing code (like the ones above) inside     lockFocus and     unlockFocus, which are called on the image. Drawing into an NSView requires you to subclass NSView, and then override its     drawRect: method, and inside that you put your drawing code. You should experiment with drawing first and get comfortable with it before you start making a big app. There is code probably on here and I know in Apple's Cocoa documentation for tracking the mouse in a drag event loop, which you will need for proper drag handling. 

----

See     /Developer/Examples/AppKit/Sketch/ for an example Cocoa app that is much like AppleWorks' drawing mode, with full source code.

