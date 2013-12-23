---
layout: page
title: RegeneratingWindows
---

I'm trying to get the behavior of, for example, iCal.  Click the red close button of the main iCal window and the calendar closes.  Now, without choosing another app, click iCal's icon on the dock, and iCal's window regenerates.  The problem I'm having is that 

     (void)applicationDidBecomeActive:(NSNotification *)aNotification

isn't working, since when you click on the dock icon, if the program is already running, the app does not *become* active (it already *is* active).  Any ideas how to get this behavior?  Thanks.

----

Look at     - (BOOL)applicationOpenUntitledFile:(NSApplication*)app;  -- Bo

----

I think - (BOOL)applicationShouldOpenUntitledFile:(NSApplication *)sender is actually what you want, or does that already return YES by default? (This is a delegate method of NSApplication, by the way, so you'll need to override this in your app delegate).

----

Or     - (BOOL)applicationShouldHandleReopen:(NSApplication *)theApplication hasVisibleWindows:(BOOL)flag. I beleive this gives you a chance to customize the default behavior, which is probably what iCal does.

----

Ack! Yes, applicationShouldHandleReopen is much better suited to the task.

