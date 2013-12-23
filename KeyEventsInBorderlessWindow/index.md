---
layout: page
title: KeyEventsInBorderlessWindow
---

I am opening a borderless panel. Normally a user can close a panel by hitting escape or command-".", but this does not work for the panel when it is borderless.

I can overload keyDown: to call interpretKeyEvents: and then doCommandBySelector: will get called (with cancelOperation:) for escape, but not command-".". Also, overloading keyDown: for the window disables keyboard support for all other potential responders in the window.

So what to do? Must I explicitly check for escape in keyDown: (and perhaps also command-".")? explains why these keyboard commands are supported so inconsistently ;-)

It seems to be related to     performClose: which fails (and beeps).     close works fine. I do set     NSClosableWindowMask for the window, have tried to implement     windowShouldClose to return     YES in both the window subclass and my delegate � but     performClose: still fails...

ahh... just saw this for     performClose: *If the receiver doesn�t have a close button or can�t be closed (for example, if the delegate replies NO to a windowShouldClose: message), the system beeps.*. And it seems the check is not made on the     styleMask � but overloading this method to simply invoke     [self close] will give both escape and command-"." functionality.

----

Normally, a borderless window can't accept key events. You need to create a subclass of NSWindow and declare:

    
 - (BOOL)canBecomeKeyWindow
{
    return YES;
}


I had this problem too, but in my case I was creating a borderless window to use at full screen. Instead of subclassing NSWindow I ended up creating my own run loop. In the case of a full screen window it made sense. Here's the code if you want it.

    
- (void)startRunLoop
{
    continueRunLoop = YES;
    
    while (continueRunLoop) {
        NSAutoreleasePool   *pool;
        NSEvent              *event;
        
        // We need to handle the autorelease pool
        // because we are in our own loop
        pool = [[NSAutoreleasePool alloc] init];
         
        // Process events
        while (event = [NSApp nextEventMatchingMask:NSAnyEventMask untilDate:[NSDate distantPast]
                                    inMode:NSDefaultRunLoopMode dequeue:YES]) {
            switch ([event type]) {
                case NSKeyDown:
                    [self keyDown:event];
                    break;
                // etc...
            }
        }

        // You might want to redisplay the appropriate
        // views here if they need to be.
        
        // Clean up any autoreleased objects that were created this time through the loop.
        [pool release];
    }
}


-- RyanBates

----
Here is a way to get key events without a window.

Create a subclass of NSApplication. Then override the -sendEvent: method in the subclass' implementation so it looks like this:

    
- (void)sendEvent:(NSEvent *)theEvent
{
    if(NSKeyDown == [theEvent type])
       NSLog(@"keyDown");
}


Next change the Target's 'Info.plist->Cocoa-Specific->Prinicpal class' to the name of the subclass.

----

You don't need to pass on the event to     super? 

----

If your object does not handle the event and wants another object down the ResponderChain to handle the event, then yes. You would want to pass it to     super which would pass it to the next object in the responder chain. In the case of the full screen window, I wanted to take over all events - not passing any to the next responder. -- RyanBates

----
Maybe this is just "cheating" (if there is such a thing), but whenever I need to capture a single keystroke (ie, 'escape') in such a window I just create an NSButton with a key equivalent of that key (usually Escape), and move the button outside of the visible bounds of the window.  This button calls whatever method is needed to take care of business, and I'm done.  /ducks

