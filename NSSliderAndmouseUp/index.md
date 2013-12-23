---
layout: page
title: NSSliderAndmouseUp
---

Describe NSSliderAndmouseUp here.
CocoaDiscussions - 

I'm trying to have an NSSlider where a QTMovie stops playing on mouseDown (but the slider's Action is still being sent while the float values are changing), and on mouseUp, the movie starts playing again. I've tried subclassing NSSlider, but mouseUp doesn't seem to be working. Any help?
----
See <http://www.cocoabuilder.com/archive/message/cocoa/2006/3/4/157961>
<http://developer.apple.com/documentation/Cocoa/Conceptual/EventOverview/index.html> Listing 4-3
<http://www.cocoabuilder.com/archive/message/cocoa/2004/5/20/107566>
<http://www.cocoabuilder.com/archive/message/cocoa/2004/8/29/115981>
<http://www.cocoabuilder.com/archive/message/cocoa/2006/6/5/165039>
----
Thank you! This helped a lot.

I'm a Cocoa noob, and I had one more question. So now I want to send an action on mouseUp (I can do things within the mouseDown method, but I'm not exactly sure how to use the sendAction method).

Right now I have:

    
- (void)mouseDown:(NSEvent *)theEvent {
	mouseIsDown = YES;
	[super mouseDown:theEvent];
        mouseIsDown = NO;
	[self sendAction:@selector(mouseEvent:) to:SliderController]; //This is giving me a parse error obviously.
}

I know I'm not using sendAction properly... but what I'm trying to do is send the (mouseEvent:) action to my SliderController. What would the correct syntax for this (I've only sent actions through IB before... never programmatically).

