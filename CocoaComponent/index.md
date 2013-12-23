---
layout: page
title: CocoaComponent
---

Hello everybody,

I'm fairly new at Cocoa programming and have a question about the new CocoaComponent class in Java 1.4.1.

http://developer.apple.com/documentation/Java/Reference/1.4.1/Java141API_AppleExtensions/api/com/apple/eawt/CocoaComponent.html

I have created a custom NSView which I want to be able to send messages to, and get return values.

I'm using the sendMessage() method to send messages to my NSView, and everything works fine until I get the case that I want to get a return value from the NSView when I click on a Java button.  I have a result class that is used for transfering the return value between threads, and it blocks the current thread until it is set.

So I end up with a deadlock, since sendMessage wants to use the AWTEventQueue, which is being blocked with the button waiting for a return value.

In the documentation for CocoaComponent it says there is a better way to message the underlying NSView, but it says no more about it.

I presume that it then sends some event to the Cocoa event queue which is how it get's to my View.  Would it be possible to create a custom event to send to my view?  I have absolutely no idea on how to do this in obj-c, I had a little look at NSWindow -(void)postEvent:atStart.  How do I create a custom event and listen for it in objective-c?  I can call a method to do this from JNI.

-Chris


Check out NSEvent for information on how to create events.

Ok I managed to create a custom event to send with type NSApplicationDefined, but I don't have a clue on how to listen for it in my custom NSView.


Also Does anybody know where I can dowload the Java 1.4.1 source for mac?  If I can look at this I'm sure I can work out how to resolve my problem.

