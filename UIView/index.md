---
layout: page
title: UIView
---



Part of the iPhone General/UIKit framework. Subclass of General/UIResponder. So very much like General/NSView.

<code>- (id)initWithFrame:(General/CGRect)rect;</code>

Designated initializer.

<code>- (void)addSubview:(General/UIView*)view;</code>

<code>- (void)drawRect:(General/CGRect)rect;</code>

Since we don't have a General/UIGraphicsContext class, General/CoreGraphics is our friend now. All those CG calls need a General/CGContextRef, and here's how you get it:

      General/CGContextRef General/UICurrentContext();

<code>- (void)setNeedsDisplay;</code>

<code>- (void)setNeedsDisplayInRect:(General/CGRect)rect;</code>

<code>- (General/CGRect)frame;</code>

<code>- (General/CGRect)bounds;</code>

<code>- (void)setTapDelegate:(id)delegate;</code>

Sets the tap delegate. See below.

**
Swiping!
**

    
typedef enum
{
	kUIViewSwipeUp = 1,
	kUIViewSwipeDown = 2,
	kUIViewSwipeLeft = 4,
	kUIViewSwipeRight = 8
} General/UIViewSwipeDirection;


<code>- (BOOL)canHandleSwipes;</code>

<code>- (int)swipe:(General/UIViewSwipeDirection)num withEvent:(General/GSEvent*)event;</code>

This one is listed in the General/UIView-General/LKLayerDelegate.h header, and the iTetris demo uses it to good effect, but it does nothing for me. Can't tell why, but I don't really get layers yet..

<code>- (void)drawLayer:(id)inLayer inContext:(General/CGContextRef)inContext;</code>

----
**iTetris follows some bad practices when it comes to drawing.  A General/UIView is transparently backed by an General/LKLayer, and the view uses this method to draw itself internally, so don't override it! Just use -drawRect: and everything will work as expected.** -- Lucas Newman
----

**
Tap delegate methods
**

<code>view:handleTapWithCount:event:<code>

<code>view:handleTapWithCount:event:fingerCount:</code>

<code>viewHandleTouchPause:isDown:</code>

<code>viewDoubleTapDelay:</code>

<code>viewRejectAsTapThrehold:</code>

<code>viewTouchPauseThreshold:</code>
