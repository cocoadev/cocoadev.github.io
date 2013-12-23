---
layout: page
title: TrackMouseMovement
---

Hey everyone, I've been stumped by this problema bit, so hopefully you can help out. I would like to track mouse movement to activate when it is placed in a certain corner or a certain gesture is made. The problem is that all things I have tried (my last approach using General/TrackMouseLocation()) only work when my application is foremost. As soon as I switch to something else, it stops listening for mouse movement. I would like to implement this without polling, but that is another option I'm willing to consider. So, is there some way to track mouse movement without polling even in the background, and if that's not an option, what sort of tips do you have on implementing polling for mouse movement so that I don't peg the processor or anything...

Thanks!

----

polling and tracking are the same thing!!

----

Well, the General/TrackMouseLocation() just blocks a thread while I wait for input. It's not polling, it's relying on a system call. That's all I'm really looking for. Just a system call that doesn't require me to poll input wastefully.

----

You'll probably need to ask Apple for support for this feature.   Background apps don't usually get events like this since it would require waking up the application to have it handle mouse moved events everything the user happened to mouse over it, which if done for everyone would be pretty expensive

----

Hmmm.. wonder if a full-screen transparent window floating over everything would let through mouse clicks but still give you the mouse motion

I agree, I think it may.


----

    General/NSPoint loc = General/someView window] mouseLocationOutsideOfEventStream];  --zootbobbalu

Right, but the point is he wants to do this in an application that's not foreground, and without having to spin in a polling loop calling this method.

----

You need to have two event tracking mechanisms 


* one for mouse movements inside your application : override -[[[NSApplication sendEvent:] or use General/TrackMouseLocation()
* one for mouse movements in other applications : sample code :


    
General/OSStatus mouseActivated(General/EventHandlerCallRef nextHandler, General/EventRef theEvent, void *userData) {

        // insert something to do here

        return General/CallNextEventHandler(nextHandler, theEvent);
}

static General/EventHandlerRef trackMouse;

General/EventTypeSpec eventType[3];
eventType[0].eventClass = kEventClassMouse;
eventType[0].eventKind = kEventMouseDown;
eventType[1].eventClass = kEventClassMouse;
eventType[1].eventKind = kEventMouseUp; 
eventType[2].eventClass = kEventClassMouse;
eventType[2].eventKind = kEventMouseMoved;
                        
// this will also call the handler when the mouse button is activated...
// you can be more specific if you want

General/EventHandlerUPP handlerFunction = General/NewEventHandlerUPP(mouseActivated);
General/InstallEventHandler(General/GetEventMonitorTarget(), handlerFunction, 3, eventType, NULL, &trackMouse);


General/GetEventMonitorTarget() is Panther-only.
Call             General/RemoveEventHandler(trackMouse);� when you're finished tracking.
