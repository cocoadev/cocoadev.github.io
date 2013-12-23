---
layout: page
title: CountingKeystrokes
---

Hello,
 
 I'm not sure if any of you were ever familiar with Project-Dolphin, now Project-Orca, just basically a project where you download a program to run and it counts how many keys you type, as a fun little statistic, nothing too useful.
 
 There never was a Mac OS X client to interface with the project, so for fun I've been trying to figure out how to go about to create one.  Right now though I am at a loss for finding a good way to perform an action upon a keystroke being pressed, with Windows you can set hooks, but with Mac OS X, I am not really sure.
 
 I would appreaciate any ideas on how to handle that aspect.

The program would sit running in the dock or top right of the menu bar, and I am not sure what class would need to be subclassed to override the keyDown event.

Any help on event trapping helpful.

Also, I don't need to know which key is pressed, just on any key press it needs to call a method to up the current count.
 
 Thanks for any assistance,
 
 Charles

----

I don't think this is possible as a Cocoa application. Your application only gets sent keyDown events when it is in the foreground, and I assume you are hoping to count all keypresses system wide.

You could poll key status using Carbon (e.g. GetKeys) but that isn't very elegant.

There may be some way of implementing this in the kernel extension, but I don't know anything about those.

If all you want is to count keypresses when your application is frontmost, then subclass NSApplication, and use the override the  - (void)sendEvent:(NSEvent *)theEvent  method. Look in NSResponder.h (I think) for constants relating to different types of event. You also have to set the Application Class in the Cocoa-Specific section of your target options to your NSApplication subclass.

SamTaylor

----

also see KeyDownCharactersOutsideOfEventStream

