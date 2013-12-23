---
layout: page
title: WaitingForMouseEvent
---



I used to program in JAVA where I could write a method that would wait until the mouse had been clicked somewhere in a desired position, and then return the coordinates in some form.  You could call that method every time you wanted the program to "pause" and wait for user mouse input.

Now, in Cocoa there is an easy way to handle mouse events, but the other way around: you specify what would happen if the mouse was clicked (mouseDown: in an General/NSView subclass).  But I'd like to keep my event handling at different places throughout my code.  For example: in my model code, I want to store many mouseclicks after eachother.  I would call a gui action (to wait for a mouseclick and return) through the controller repeated times, and save the locations.  That allows me to use while loops etc, to wait until a certain number of mouse-events have occured and then return to the normal application state.
I've tried to do this by subclassing mouseDown: and using a boolean to see if the last click location has changed.  But this causes my program to crash, probably because one method loops until the boolean has changed, and that boolean can only be changed by another method in another class, which can never be called, apparently.
So is there a way to do event handling the way I want it with Cocoa?

----

Wellll, not really.  You'd never want to say something like "wait for a mouse click" on the main thread, because the program would be unresponsive until that mouse click occurred.  This is the whole point of Cocoa's asynchronous style - we never poll, we use callbacks.  

But, if you want to distribute your event handling logic, you may be able to use notifications.  In your mouseDown: method, issue some notification and include any relevant info.  In another part of your code you can register for the notification at times when it interests you.

If you really, really, want to avoid exiting a method until a mouse click occurs, you'll have to advance the run loop yourself within the method.  And please think hard about other options first, especially if you're coming from Java ('cause Cocoa has its own ways of doing things, and you want to write good idiomatic Cocoa code).  See General/RunLoop.
