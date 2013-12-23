---
layout: page
title: MakingInspectorWindows
---

What's the best way of making a shared inspector window like IB's inspector?  I've looked at NSWindowController, but that seems to be for "extra windows per document".  If I have three windows, I don't want three inspectors, but just one that changes when the current document changes.  I haven't seen a "Document Change Notification" to know when to swap out the contents.  How would I go about doing this?

*
I did this by creating an app controller class (MyAppController) which is instantiated in MainMenu.nib. MyAppController is also NSApplication's delegate (connected by dragging from file's owner to the instance of MyAppController). I then added accessor methods to MyAppController to return the window controller for each inspector. When a document becomes the foreground doc it can ask for the inspector window controllers like this:

MyInspectorController *theInspectorController = ([[MyAppController *)[NSApp delegate] myInspectorController];
*

I sure hope you didn't really use MyAppController for your class name...

*
Why not? I think it's a good class name.
*

----

Check out MakingNibsTalkToEachOther, which is almost the exact same problem.

----

Except for the "changing the contents of the inspector when a new document window comes to the front".  Is there a notification about when that happens?

*Yeah, NSWindowDidBecomeMainNotification, and NSWindowDidResignMainNotification. Just put code in your document window's delegate to update the inspector window when either notification occurs.*

----

Check out http://www.borkware.com/rants/inspectors/ for a pretty detailed explanation of another approach.

