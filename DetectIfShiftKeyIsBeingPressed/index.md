---
layout: page
title: DetectIfShiftKeyIsBeingPressed
---



I have one of those nifty smooth effects in my app and was wondering If I can detect if the shift key is being held so I can slow it down.

----
NSView is a subclass of NSResponder. You want to look at the     flagsChanged method, or you can do something like this.

    
    NSEvent *currentEvent = someView window] currentEvent];
    unsigned flags = [currentEvent modifierFlags];
    if (flags & [[NSShiftKeyMask) NSLog(@"shift key has been  presseed");


[RTM]

----

Actually, its a bit easier. The NSWindow instance just gets if from your NSApplications instance. This **should** work:

[NSApp currentEvent];

    
    NSEvent *currentEvent = [NSApp currentEvent];  // NSApp is a global pointing to your application instance
    unsigned flags = [currentEvent modifierFlags];
    if (flags & NSShiftKeyMask) NSLog(@"shift key has been  presseed");


----

I believe in being very careful with bitmask operations. I'd write it like this:

    
    NSEvent *currentEvent = [NSApp currentEvent];
    if ([currentEvent modifierFlags] & NSShiftKeyMask == NSShiftKeyMask) {
        NSLog(@"shift key has been pressed");
    }


*if you do it that way, don't forget a = ;)*

