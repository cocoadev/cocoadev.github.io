---
layout: page
title: GestureActions
---



I'm trying to build a (background?) cocoa application that will allow me to send events to the current application based on key presses and mouse movements.

The idea is to use the gesture stuff from the opera web browser on osx.  It's incredibly addicting.

Anyway, I'm seaching through documentation, trying to figure out how to do this, and I'm running into a roadblock as far as getting mouse down / keydown events and responding to them while my application is in the background.

Is there a cocoa way to do this, or do I have to use CarbonEvents ?

thanks,

GusMueller

----
There really isn't a way to get events while in the background from Cocoa. I'm pretty sure CarbonEvents will do this to some extent though. I'm not sure if CarbonEvents will let you post events, but if they don't you can look at CGRemoteOperation.h in CoreGraphics for a way to repost UI events to the system.

-- MikeTrent

----

Thanks Mike.
I managed to get the stuff in CGRemoteOperation.h to send keystrokes to the front application, so it looks like I'm off to a halfway good start :)

Now I just need to get the carbon events from inside my cocoa application.  Apple support tells me that I need to look at RegisterEventHotKey and UnregisterEventHotKey in CarbonEvents.h .  Hopefully I won't be up too late this evening working on this.

-GusMueller
----
Why don't you try Apple's Accessibility API? It lets you see what the mouse pointer is over, and even affect them.
-- MikeManzano

