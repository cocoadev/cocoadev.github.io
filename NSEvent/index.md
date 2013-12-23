---
layout: page
title: NSEvent
---

THIS RESPONSE DOES NOT WORK!!

Object carrying data about a user action, for example mouse clicks or keyboard presses.

Anybody know how to send a fake mouseUp event to a window or view?
- if you actually want to fake the mouseUp using the below though, you'll have to post the event using postEvent:atStart:

----

I would check out this NSEvent class method. 

 
     int type=NSLeftMouseUp;              // for a left mouse up event type
     int windowNumber=[windowToSendEventTo windowNumber];
 
 id fakeMouseUpEvent=[NSEvent mouseEventWithType:type
         location:NSMakePoint(xValue, yValue)
         modifierFlags:nil 
         timestamp:nil
         windowNumber:windowNumber 
         context:nil
         eventNumber:nil 
         clickCount:1 
         pressure:nil];
 
 // On NSEvent
 +(NSEvent *)mouseEventWithType:(NSEventType)type 
         location:(NSPoint)location 
         modifierFlags:(unsigned int)flags 
         timestamp:(NSTimeInterval)time
         windowNumber:(int)windowNum 
         context:(NSGraphicsContext *)context 
         eventNumber:(int)eventNumber 
         clickCount:(int)clickNumber 
         pressure:(float)pressure 
 
----
If you want to watch all the events in your program,  use NSTraceEvents.

See http://developer.apple.com/technotes/tn2004/tn2124.html for lots of cool tricks.

Watching events go by can be very informative.

----

To bypass event-handling and forward it to the NSApp's delegate (I find this very useful in fullscreen games), make a custom subclass of NSApplication (ensuring that the Principal Class is your new subclass), and implement this method:

 - (void)sendEvent:(NSEvent *)event
 {
     if (self delegate] shouldHandleEvents])
         [[self delegate] handleEvent:event];
     else
         [super sendEvent:event];
 }

Of course, this assumes your delegate responds to shouldHandleEvents and handleEvent:.  Going back to the fullscreen game example, this allows for one to have a Cocoa main menu and have the delegate (controller) handle the events appropriately, thus allowing true MVC.  This can also allow for the same controller handle a windowed game, too. -[[RossDude

*To be strict about whether the delegate responds, you should check first with     respondsToSelector: or     conformsToProtocol:*
----
How to watch all the mouse events of the system? (NSTraceEvent is about one perticualr application).
----
Google for CGEventTapCreate.

----
Link to the replies on working with the modifierFlags from a NSEvent

DetectIfShiftKeyIsBeingPressed
----
NSEvent timestamp is in time since system startup. NSDate doesn't give a convenient utility to get that, and it's not easy to find.
After much digging I found this in an old cocoa-dev mailing list post:

 #import <mach/mach_time.h>
 #define SKWTimestamp() (((double)mach_absolute_time()) * 1.0e-09)

