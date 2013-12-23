---
layout: page
title: MultiTouchTrackpad
---



This page describes the private API for working with the multi-touch trackpad. An in depth discussion is available at my blog here: http://cocoadex.com/2008/02/nsevent-modifications-swipe-ro.html

The main API involves three methods added to NSResponder:


* -(void)magnifyWithEvent:(NSEvent *)anEvent
* -(void)rotateWithEvent:(NSEvent *)anEvent
* -(void)swipeWithEvent:(NSEvent *)anEvent


These three methods correspond to magnify (pinch), rotation, and swiping respectively.

Each type of event has certain properties that are important. These are all methods on NSEvent.

**Swipe**

* -(float)deltaX; Either -1.0 or 1.0 depending on left or right swipe.
* -(float)deltaY; Either -1.0 or 1.0 depending on down or up swipe.


This is inaccurate. A swipe starting at the center of the trackpad and moving left (left swipe) returns a deltaX of 1.0 and a right swipe a deltaX of -1.0 (late 08 MacBook Pro).

**Magnify**

* -(float)deltaZ; Float value with some relation to NSEvent's -(float)standardMagnificationThreshold


**Rotation**

* -(float)rotation; Float value with some relation to NSEvent's -(float)standardRotationThreshold


These methods are included in AppKit starting at version 949.18.0, a.k.a. 10.5.1 on the Macbook Air, and presumably beyond.

--ElliottHarris

----
Just a disclaimer: You may not be safe in presuming these methods will exist in the future. Please note that Apple must reserve the right to change any private API in the future for our and their own benefit. I'm all for experimentation and exploration, but private API, especially as new as this, should not be used in any shipping code.

----
I generally agree that private APIs should not be used it shipping code, but in this case it's hard to see the harm. These aren't methods you call, just methods you implement. If they're changed, then your implementations will no longer be called. That just means that your multitouch support stops working, which is not a big problem. Using private APIs is bad, but implementing private methods which cause no harm when they are no longer called is not dangerous.

----
IMHO this API seems very short-sighted and tied to very particular usage patterns.  I hope that the full multi-touch information is exposed elsewhere (in HID?), for example, to allow a musical keyboard where the multiple positioning and motion of each touch affects a voice within a sound...

----
I agree, but it's worth thinking about *why* there would be a limited API. The main reason is so that developers are encouraged to make sure the same gesture means the same thing in different apps. (Think about operator overloading; if you override + to perform bitwise OR, you're going to confuse and annoy the heck out of most people.) On the other hand, the fact that you can't use raw touch data is annoying...a keyboard app could be cool if implemented well. --JediKnil

----
Doesn't the iPhone provide raw touch data that you then interpret as you please? Seems strange that they'd do it one way there and a different way here.

----
FYI: In Snow Leopard (10.6) MultiTouch gestures are no longer private API, NSResponder class supports touch and gesture events - http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ApplicationKit/Classes/NSResponder_Class/Reference/Reference.html#//apple_ref/doc/uid/20000015-SW35

