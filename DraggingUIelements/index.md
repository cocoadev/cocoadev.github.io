---
layout: page
title: DraggingUIelements
---

I am facing an extremely annoying problem that I hope some brainy individual could help me sort out.

I have an app with an NSView subclass, call it oldView.

This NSView has a method addView that adds a view in the first view. In exact addView adds an NSView of a subclass, say newView. In oldView I have implemented mouse functionality that makes it possible for me to drag my added newViews around.

 Now, everything works dandy as long as I do not add any UI elements to my newView�s. Adding any UI elements, like buttons, scrollviews or even nib elements make my views behave peculiar. Dragging the views around leaves a shadow of the UI element in oldView (only UI elements, not the rest of the view). This of course is sorted out by a setNeedsDisplay in my oldView or by setting a solid background, but it is somewhat annoying. What is worse is that the UI elements may show through my newViews if they overlap! In my application I need to be able to drag UI elements or views from nibs around in a particular view.

I have tried various methods to solve this problem, but I am lost. I have a few workarounds that may work, but I am curious about whether others have faced the same problem and hopefully solved it?
----
I considered answering on Apple's Cocoa mailing list where this was posted, but I blew it of as an RTFM problem.
1) Overlapping sibling views are explicitly not supported by Cocoa at this time.
2) setNeedsDisplay: is the correct way to mark Cocoa a view as needing to be re-displayed the next time it is convenient for the framework to do so.  There is no reason for the OP to be annoyed doing it.

