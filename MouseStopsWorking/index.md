---
layout: page
title: MouseStopsWorking
---



I have a problem with my first Cocoa app.  In one of the windows, which contains a few buttons and text areas (all standard), the mouse sometimes stops working.  The keyboard continues to work in that window, and other parts of the application respond fine (menus, drag and drop in another window).  I have no idea how to debug this.  I've searched online, I've tried to figure out where mouse clicks are going, and have had no success.  The behavior is not consistent.  It often starts happening after the program does a large batch of computation, but not consistently.

How would I even approach debugging this?  Can I monitor mouse clicks earlier than the buttons and see where they are going astray?  

----
Are you sure it's not actually a problem with your mouse?  I've never heard of such a thing happening because of anything Cocoa does.

----
Yes, the mouse is definitely working.  The application has three windows, and the other windows continue responding to the mouse as they had before.  It's just the one window's buttons and text fields that stop doing anything when clicked.

----
I have seen similar symptoms to this when I had accidentally divided by 0, and then passed the resulting NaN into an NSScroller. Apparently giving nonexistent numbers to views can really screw up event processing and the view hierarchy. Try to figure out exactly what you do that causes the problem, then trace through the code that runs and see if there's anything fishy happening such as this.

