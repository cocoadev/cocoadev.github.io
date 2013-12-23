---
layout: page
title: MultiTouch
---

MultiTouch, the IPhone touch screen interface.
IMHO, the most exciting development in recent computer history. This is StarTrek(c) folks. How long before we find this on a MacTablet? I think very soon.

NSEvent is updated (since 10.3.9?), look at http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSEvent_Class/index.html#//apple_ref/doc/uid/TP40004039 and notice the new NSTypeProximity info. Exciting! I wonder how this will affect Cocoa code. My brain is spinning with the possibilities this offers.

--JeremyJurksztowicz

----
Not to put a damper on things, but NSEvent's proximity stuff has to do with the proximity of a stylus to a tablet, not the proximity of two different pointing devices being used simultaneously. As far as I can see, NSEvent currently has no MultiTouch capabilities at all.

----
An interesting video of the possibilities: http://youtube.com/watch?v=UcKqyn-gUbY

----
Well, NSEvent does have 'pointingDeviceID' which could be used for MultiTouch. You would get a stream of NSEvents from each of the pointingDeviceIDs and each would have a different x and y. --ZacWhite

Er, that's device as in hardware device isn't it? So that would be the multitouch screen, then, not my fingers? -- RobRix

Well - you could imagine that the pointingDeviceID could be virtual and generated upon each new touch point on the screen... possibly with a  new event to say pointingDeviceID finished... Guess we will just have to wait and see if/when this comes to the desktop. --RbrtPntn

----
Well, since we're on the subject, you may be interested to know where Apple picked up this technology.  From what I can tell, Apple either bought out, or hired the inventors running Fingerworks ( http://www.fingerworks.com ), a company which produced multitouch gesture pads and keyboards.  

You can still find some of their products on ebay, but not at rational prices.  Those who have them aren't letting them go.  I still have an iGesture pad, myself, and won't even consider selling it.

I really hope Apple puts this technology to good use, and begins applying it to products beyond the iPod/iPhone line.

--AndrewBowman

----
Apple *has* put it to good use in the new Macbook Air, and presumably will continue to put it into their laptops. You can find out more information about making your application Multi-touch capable at MultiTouchTrackpad

--ElliottHarris

