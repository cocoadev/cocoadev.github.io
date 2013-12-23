---
layout: page
title: TransparencyMasksThroughToSubviews
---

If I have a view with subviews (call masterView: A and the subviews, B-E) what I'd like to do is subclass A's drawRect and some how do:

    
- (void)drawRect:(General/NSRect)rect {
  [super drawRect:rect];
  [myAlphaMask compositeToPoint:General/MyLoc operation:General/NSCompositeSourceOut];
}


myAlphaMask is effectively a black circle with the alpha set on a gradient from 0 to 1.0 from radius r to radius 0. 

The idea is that myView will have some arbitrary subviews in it. On a mouse down it will get the mouse click point. Then fade all the subviews from the mouse point to the background in a circular splay. Sort of like a spot light, but instead of things getting darker outside the spot light center they fade away.

I want to handle this in myview as the subviews are moving around in their own thread and just want to draw themselves without knowing what is going on in the top layer.

----

The superview's drawing method will normally be called before the subviews'. Superviews can't draw over their children without a fair bit of naughtiness. One idea would be to add another view that's always on top that composites the background on top of overthing else appropriately.
