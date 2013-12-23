---
layout: page
title: DisablingTabViewItems
---

perhaps this is just not part of the l&f guidelines for aqua (if so, please tell me), but i can't seem to find a way to have a disabled tab view item. in otherwords, let's say i have a tab view with 3 tabs; i want to have the third tab be disabled (one would think the font colour for the tab title would then be the aqua l&f disabled menu item foreground colour).
the best i seem to be able to do (in a somewhat gross design way), is to be the delegate of the tab view, and intercept the selection via:
tabView:shouldSelectTabViewItem:

this obviously feels quite wrong, as other l&f seem offer the concept -- and i can get the behaviour using java under aqua.. ?

----

Is it advisable to go against the guidelines? I know I have not seen one application (except I think the horrendous Limewire) have disabled tabs. Why not simply remove the tab item if someone can't click on it, although that goes against another set of rules for human interface design.

----

Tabs that disappear / reappear or are disabled are going to confuse the heck out of users.  That's VERY non-standard behavior for a Mac OS app.  The fact that it's so hard to implement in code has tipped you off that something is wrong.  There Must Be A Better Way To Do It.  I'd recommend a re-think of your GUI design.  

----

Why not just leave the tab view enabled but disable all its contents?

----

On a related matter, the AHIG says: *If all of a menu�s commands are unavailable (dimmed) at the same time, dim the menu title. Users should still be able to open a dimmed menu to see its contents.*

It is the latter part I wish to emphasize (as the part of dimming the title is actually not possible with Cocoa's automatic menu validation). I think the thinking is something along the lines of: *do not hide info (in the form of menu item titles) from the user, just because he is headed for a dead end, but make it visually clear to the user, that this is in fact a dead end.*

By disallowing the user access to the tab item, you limit *end user innovation*, e.g. he may be helping a friend over the phone, and needs to see which options the page holds, without wanting to go through the steps to enable it or similar � you can't really know, and that is why I say *end user innovation*, cause it covers all the cases you did not imagine would be done by the end user, when you designed the program.

