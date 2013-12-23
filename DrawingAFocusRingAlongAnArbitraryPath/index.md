---
layout: page
title: DrawingAFocusRingAlongAnArbitraryPath
---

I have a document view that needs to a draw focus ring (non rectangular) along predetermined path that represents a focused sub-content outline (NSBezierPath).

How do I actually draw a standard looking focus ring along such a path?

----

    
	[NSGraphicsContext saveGraphicsState];
	NSSetFocusRingStyle(NSFocusRingOnly);
	[path fill];
	[NSGraphicsContext restoreGraphicsState];


----

You may find that the focus ring partially falls outside of your target view's bounds or the current context's clipping rect (see below for additional details on focus ring clipping). In that case, you will typically need to insert a line, so that your code looks something like this:

    
[NSGraphicsContext saveGraphicsState];
NSSetFocusRingStyle(NSFocusRingOnly);
[[NSBezierPath bezierPathWithRect: NSInsetRect([self bounds], -8.0f, -8.0f)] setClip];
[path fill];
[NSGraphicsContext restoreGraphicsState];


(If there's a less hacky way to handle oversized focus rings, I'm not aware of it.)

----
Having the focus ring fall outside the view's bounds is supported and often desired. Look at what happens when you focus on an NSTextField, for example.

----

A focus ring isn't clipped by the currently focused view as stated above, it is however clipped by the super view's bounds.

