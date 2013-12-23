---
layout: page
title: AutoScrolling
---

I thought I'd share something I just learned about autoscrolling.

Let's suppose you have an NSView and are implementing the mouseDown:, mouseDragged: and mouseUp: methods. The naive way to autoscroll is to call [self autoscroll:event] in the mouseDragged: method. This only partially works - it will autoscroll, as long as the mouse is kept moving.

The (presumably correct) method to implement autoscrolling, as mentioned but not fully explained in Hillegass, is to create a timer on mouseDown which is removed on mouse up. The timer's callback calls autoscroll with the current event. The timer callback looks like:

    

- (void)   timerAutoscrollCallback:(NSTimer*) timer
{
    NSView* view = (NSView*)[timer userInfo];  // this is one way to pass along the view - you can also use a data member in the class that implements this or use self if it's in the view

    [view autoscroll:[NSApp currentEvent]];
}



This works great apart from one weird thing which I'll mention in a sec. This scrolls the view as long as the mouse is outside the visible part of the frame, whether the mouse moves or
not. A timer period os about 1/10th sec seems about right.

The weird thing I found was that once in a blue moon, when I let go of the mouse at the end of the drag, the view would jump at random to another location. It would only happen maybe one time in fifty, but when it did it was extremely annoying - especially if you were carefully positioning something. I checked and checked my code, but couldn't see anything obvious amiss. So I decided to log the event that caused the autoscroll, and lo and behold, when the errant autoscroll occurred, the event that caused it was an NSSystemDefined type, NOT the NSLeftMouseDragged which was the usual. I have no idea what this event is or who sent it, but the fix is easy - just perform the autoscroll ONLY for the specific type of event  you need (usually NSLeftMouseDragged), don't assume [NSApp currentEvent] is really what you think it is.

Fixed version:

    

- (void)   timerAutoscrollCallback:(NSTimer*) timer
{
    NSView* view = (NSView*)[timer userInfo];  // this is one way to pass along the view - you can also use a data member in the class that implements this or use self if it's in the view
    NSEvent* event = [NSApp currentEvent];
    if ([event type] == NSLeftMouseDragged )
        [view autoscroll:event];
}



I do hope this tip (which may be obvious to veterans) will help somebody because this problem has been bugging me for a while! --GrahamCox

----

Note that although the above fixed version works beautifully, Apple recommends using NSPeriodic events to handle autoscrolling. See for instance:
http://developer.apple.com/documentation/Cocoa/Conceptual/EventOverview/EventObjectsTypes/EventObjectsTypes.html (bottom of the page)
--Daniel

