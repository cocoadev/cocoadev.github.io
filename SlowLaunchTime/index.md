---
layout: page
title: SlowLaunchTime
---

I have a nice, small application (under 11k of code) that has one window.

I don't have it do anything on startup, but it takes about a second after the window is drawn on the screen for it to become responsive (when the window comes up, a text field is active - I can type a few letters, but the window won't update for about a second. After this, it becomes totally responsive.

Any ideas on optimizing or whatnot? I'm new to Xcode and Cocoa.

Thanks!

----

If this is a single-nib app (the window is in MainMenu.nib) move the window into its own nib, with NSWindowController as FilesOwner, and load it as appropriate. Also make sure your     init and     awakeFromNib methods are as efficient as possible.

----

If you've just been testing this on your own computer, you might want to turn off ZeroLink. ZeroLink trades off less time when compiling the app for more time when running the app, so it can make your app start up slower. I don't know if that's your problem, but it's something to try. Another thing to do would be to run your app in Shark and actually see what's taking up so much time when you start it.

----

What I'd do first before making too many changes to code or nibs is to use the various tools available to find out what's happening.  Like run the program in the debugger and interrupt the program and look at the stack trace during this start up time.  Use Sampler or Shark to profile the program and see where the time is going.  Run the Activity Monitor when your program is starting up.  Is the CPU pegged?  Is CPU low but disk activity high?  Also look for things that can take a long time under some circumstances, like are you doing some kind of networking stuff on startup that's waiting for a timeout, or does the machine have network drives mounted that may be clogging up the works.  For a point of reference, on my first generation tiBook, small Cocoa apps launch and are responsive nearly instantly, so it's not slowness inherant in the toolkit.    ++MarkDalrymple

