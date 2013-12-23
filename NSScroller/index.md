---
layout: page
title: NSScroller
---

The NSControl implementation of scrollbars. These are generated and configured automatically by NSScrollView. In principle, you shouldn't have much reason to access one directly.

----

If you want to change the size of the scroller on a NSScrollView being drawn via code you have to attach an instance of NSScroller on to it.

----

I want to use a NSScroller in my app to control something inside another control. I don't want to use it as a part of a NSScrollView, thus I would apreciate some help on how to use these things.

FelipeBaytelman

----

It's pretty straightforward to use an NSScroller all by itself, but there are some potential stumbling blocks it can be good to be aware of. I assume you use IB and hook an action up to the scroller and let the view you want to control receive it. Let's call this action "scrolled". The most important thing is that it isn't enough just to read the value of the knob of the scroll bar and use that, you'll have to check the "hit parts" as well. A template might look like this:

    
- (void) scrolled: (NSScroller *)scroller
{
    float knobPosition;

    switch ([scroller hitPart]) {
        case NSScrollerIncrementLine:
        // Include code here for the case where the down arrow is pressed
            break;
        case NSScrollerIncrementPage:
        // Include code here for the case where CTRL + down arrow is pressed, or the space the scroll knob moves in is pressed
            break;
        case NSScrollerDecrementLine:
        // Include code here for the case where the up arrow is pressed
            break;
        case NSScrollerDecrementPage:
        // Include code here for the case where CTRL + up arrow is pressed, or the space the scroll knob moves in is pressed
            break;
        case NSScrollerKnob:
        // This case is when the knob itself is pressed
            knobPosition = [scroller floatValue];
            // Do something with the view
        default:
            break;
    }
}



Also, note that for all cases but the last, you will have to write code to move the scroller knob yourself. The knob is set by

    
[scroller setFloatValue:knobPosition knobProportion:proportion];


where knobProportion is the size of the scroll bar, ranging from 0.0 to 1.0 (1.0 fills out the slot).

Finally, don't forget to mark the scroller enabled, otherwise it will just end up without arrows and a knob, no matter what you try. You will want to avoid that headache:

    
[scroller setEnabled:YES];


- GunnarSpindelkrok

----

And how did one get IB to do this setup?  There is no NSScroller in the palette.  Does one just do a custom view and set the class to NSScroller?

Charles

----

In the menu bar: Layout > Make subviews of > Scroll view

----

But that makes a normal NSScrollView. If you just want an NSScroller my guess would be as you said, insert a CustomView and set its class to NSScroller, since NSScroller is a subclass of NSView. Since IB doesn't support NSScroller like the others, you'll have to set the size to be the right width or height yourself. NSScroller seems to automatically work out whether to be a horizontal or vertical scroller based on the bounds you give it.

----

I am having a little bit of trouble implementing a CustomNSScroller and would appreciate any help.  I have searched the docs and Google for help but have come up empty handed.  It really is only two small problems that need to be resolved, otherwise it is working perfectly.  Thanks in advance!

----

I want to subclass NSScroller and use it in a WebView, so I can change the look of the scrollbars (at very least the size). I know WebViews usually have lots of frames, but I'm injecting the HTML, so I have control over that. I can get the NSScroller and NSScrollView objects just fine, but I can't figure out what to to from there. "- (void)setVerticalScroller:(NSScroller *)aScroller" doesn't seem to work. I've subclassed with Interface Builder before just fine, but I can't seem to understand how to do it programmably. This is about all I could find: http://lists.apple.com/archives/webkitsdk-dev/2003/Dec/msg00004.html.

