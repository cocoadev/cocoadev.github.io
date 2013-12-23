---
layout: page
title: ManageWindowsInAnyOpenApp
---

OK I'm learning Cocoa because I thought it would be fun, but theres an app I've really needed for a long time now and I dont even know if its possible to create.  It really should be built into OS X but it's not (obviously).

What I need is a simple Dock item, or menu extra, or contextual menu, or a flying saucer that hovers over my computer for all I care, that will let me pick an open window, and set its location to 0,0 or 10,10 or whatever.  Maybe this could even be set in a prefs pane or something.  The reason I need this is that there are a few OSX applications (Ahem... Adobe) that arent aware that a second monitor has been unplugged.  I daily use my powerbook with a second monitor attached, but then when I use, for example, Photoshop when I dont have the second monitor, certain windows like the styles & effects dialog will show up positioned for the second monitor and I cant get to them without trashing my prefs.  Same for GoLive & a few other applications.

Is there any kind of system wide access to all the windows so another app could bring up a list of every open window, or even better, filter it by application, and allow you to relocate it os you can actually get to the darn thing?

I'm still pretty far away from the point where I could write somethign like that but it'd be nice to know I have hope loming on the horizon.

Thoughts?  Opinions?  Other options?

CliffPruitt

----

You can use AppleScript through the NSAppleScript class to get information and control windows in applications that are scriptable - I don't know whether the ones you are interested in are. If AppleScript doesn't work then your only real option is to make something like an ApplicationEnhancer module that loads itself into the other applications to control there windows from within. There are also private CoreGraphics calls that could do what you want from within your own application, but it's hard to get these to work (you have to get your application registered as the Dock).

Sam Taylor

