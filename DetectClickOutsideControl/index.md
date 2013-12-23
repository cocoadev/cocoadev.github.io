---
layout: page
title: DetectClickOutsideControl
---

I've been implementing a custom control as a pair of NSActionCell / NSControl subclasses and have hit a bit of a snag.

My control displays a drop-down box similarly to an NSComboBox, which I want to dismiss if a mouseDown action is detected anywhere outside my controls cell. However I can currently only track mouse actions originating in my cell. I did think that a notification might be posted when clicking on the window, but I can't find one.

Can anyone tell me how I would go about detecting this event from within my cell? 

---- 

In your case, it's better to play with first responder status of your control (or of views used in control's cell) by overriding **resignFirstResponder** method.
If your control is the first responder (after acting on it), clicking outside (or other events like tabbing between controls) makes another view the first responder. So, your control loses its first responder status. 

---- 

I don't think that's quite right. An NSComboBox in a standard window with it's drop-down area visible will not resign it's firstResponder status when the window is clicked on, but it will hide the drop-down area. This is the behaviour I'm trying to emulate. Moving to another control (via mouse or keyboard) is working fine, but detecting this outside-click is not.

----

The mouse tracking loop approach: 

http://developer.apple.com/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/chapter_5_section_4.html#//apple_ref/doc/uid/10000060i-CH6-SW4

----

I'm definitely at a total loss here. I have an NSCell inside an NSControl. The NSCell has a button which adds a child window (an NSWindow subclass) to the controls window. This is all very much in the same fashion as the NSComboBox. I want to know when anything outside my control or the child window is clicked on, so that I can hide the child window.

Currently, I can only detect changes to the firstResponder (ie. through resignFirstResponder). Playing with canBecomeMainWindow/canBecomeKeyWindow doesn't seem to help and I don't think I can use mouseDown: calls because I want to know about events originating outside my control.

So please, does anyone have any other ideas? 

----
The mouse tracking loop approach cited above will allow you to get mouse down events that are outside of your control. It will not allow you to get mouse down events that are outside of your app, but it's trivial to detect when your app loses focus. Did you have a specific question about that suggestion?
----

Because this is an NSActionCell subclass which doesn't seem to have a mouseDown: method, I tried implementing the mouse-tracking loop in trackMouse:inRect:ofView:untilMouseUp: . I've got it to pick up clicks anywhere within my app, but while the loop is running it blocks all other events. Does this mean I'm going to have to learn how to use threads to place the tracking loop on a separate thread to the app?

----

Blocking other events is intentional and generally what happens when these things are going on. If you have timers that need to keep running while this is up, you can add them to the event tracking runloop mode. If you really want everything to keep going, you can run the runloop in the default mode.

----

Hmm. I'll keep playing with it for a bit. Here's some more details just in case :

http://tinypic.com/333lwd3.gif

Clicking the button to the right causes the child window to be added (it's an NSWindow subclass, regardless of what the screenshot says). I'm aiming to dismiss the child window on mouseDown outside either itself or its parent control. You can probably see the point of the control and how it should behave from the screenshot.

When using NSDefaultRunLoopMode I can interact with the control, but not the NSDatePicker inside the child window. Anyhoo, I'll keep playing with it for now.

----
That's because you're stealing all of the events for yourself with the custom event loop. If you want to be able to interact with controls in the child window, you need to manually detect when an event occurs that should be targeted on that window, and then do a     [window sendEvent:event] to make it process the event.

