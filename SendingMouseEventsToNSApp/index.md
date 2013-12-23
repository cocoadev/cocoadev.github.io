---
layout: page
title: SendingMouseEventsToNSApp
---



Suppose you want to cause a mouse event, without actually touching the mouse.  A simple working code example is hard to find.  Does anyone have an example that could be cut and pasted, so those who want to use this feature of cocoa could at least have something that actually works to begin their own customization?

A possible reason why someone may want to use this feature:  Lets say my mouse tried to exceed the maximum or minimum horizontal or vertical bounds of my customView.  Upon the mouse trying, my code would reset the mouses location, which would teleport the mouse to the other side of the customView.

----

Do you want to send mouse events system-wide? Then you would not involve NSEvent or NSApp, both of which are local to a given application. Instead, take a look at the CGRemoteOperation.h file. It's not well-documented, but it has functions for faking events, and they aren't extremely difficult to use.

----

And, while I'm asking, is there a way to disable the mouse events?  Why?  I want to turn off the mouse while I use word processors, because the track-pad doesn't filter out unintended input, which is very disruptive if you turn away from the screen while you type.  Upon returning, you're glad you have an undo manager. When I'm done typing, I'd like to be able to enable the mouse.

----

Have you tried checking "Ignore accidental trackpad input" in System Preferences?

----

I tried the "Ignoring" feature, but it still thinks I want to select huge amounts of text and replace them with what I'm typing in when I'm not looking.  And by the way, my undo doesn't work here, so I really have to be carefull not to look away.


----

If you want to stop all mouse events your going to have to overload NSApp. Once you get that override the -(void)sendEvent:(NSEvent*)event function. In there you want to test the event type to see if is one of the ones you dont want. If you dont want it, throw it away, if you want it to pass on ( only block the mouse stuff, there's a lot of other stuff that comes though there) send it to [super sendEvent:event]; Good luck with you editor. -- CurtisHoover

