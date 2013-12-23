---
layout: page
title: JustStartFromScratch
---

*Though it is difficult for beginners to spot, some bugs beg to be interpreted as a corruption of your InterfaceBuilder or XCode project files:*

After enabling zoom (and resize) in IB, resizing my NSWindow causes the window to immediately jump in width by about 50 pixels.  Resizing wider than this works as expected, but the window will not resize back down to its original size (always about 50 pixels or so wider).  I have the min width/height settings set to the current window size, as shown in IB.  I'd like to get rid of this initial jump in width and have the window resize smoothly down to the original size.  This occurs even when testing the interface in IB.  Has anyone seen this before?  Thanks in advance.

I cannot isolate ANY code that could possibly cause this effect. The handling of the control objects (a table, some buttons) is nothing too exciting.  The window does have a drawer if this makes any difference.  

I'm not sure that any of this helps narrow it down.  I know that if you'd seen a window jump unexpectedly when resizing, you'd probably know exactly what I was talking about.

**Never mind.  I trashed the old window in the nib file and rebuilt a new one - works as expected.  Don't know what was going on, must have been storing a faulty value for minimum width or something.**

see also NSWindowFrameAutosizing

----

*Sometimes all it takes to JustStartFromScratch is restarting your machine:*

When I first installed DevTools under Tiger I was prompted that there was a new reference file, or some such thing.  I downloaded it and it istalled. Now, in XCode 2.0, when I call up language reference and do a search I'm not finding things the way I used to.  Simple searches, like NSPoint or NSRange, for example, turned up nothing.

**I had it all set up right (configure options set to objective-c, api search) but it just was not working properly. After a restart it seems to be functioning just fine.**

----

Sometimes you may get weird compile errors and stuff when you are DebuggingCrash. Often just quitting and relaunching Xcode and/or cleaning your targets fixes these problems.

----

Sometimes DragAndDrop mysteriously stops working in an app I've been hacking at.  Restarting the computer seems to always fix the problem.

