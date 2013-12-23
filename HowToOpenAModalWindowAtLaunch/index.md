---
layout: page
title: HowToOpenAModalWindowAtLaunch
---

I'm trying to port an application to Cocoa, but I'm kinda new at it.

I want to make a launch sequence like this:

1) app starts up.. initializes menus..

2) registration checking routine checks if app is registered. If not, disable ALL menu items and open a modal window asking for the reg code 

3) user successfully closes the registration modal session

4) normal multi-document app behavior opens an empty document. 

I've read the BookLearningCocoa already; now I need a little help with the above launch sequence

----

First, find yourself a better book. BookCocoaProgramming by Aaron Hillegass would be a good one. But here's some quick notes:

Implement applicationDidFinishLaunching in the application delegate.  Do your registration check there.  To run a modal dialog, look at runModalForWindow in NSApplication.  For the menu stuff, dig into the NSMenu and NSMenuItem docs, particularly validateMenuItem.

Hope this gets you started. But the Hillegass book will give you a much better understanding than any tips I could give here.

----

I created a new document-based project and created the Registration Window class in the main nib file. I clicked "Set Visible on Launch" and all worked almost as I expected. At startup the Registration Window appeared. I set the modal window call in the "awakeFromNib" routine of my Registration Window class. This works the first time, but when I close the Registration Window it re-appears (with all the menus drawn) and I have to close it again.

So I tried again but with the "applicationDidFinishLaunching" method...

The problem now: almost all menus are disabled, including the quit menu, hide menu etc..

After reading a lot of online tutorials, I got managed to do all the things I wanted, except for one:

I open the modal window "Registration Dialog" after the app finishes launching, but if I open a document at startup, it opens the window parallel to the registration dialog.

How can I stop the app from loading documents dropped on the app icon until the registration dialog has been dismissed?

----

Add this to application delagate.

    -(BOOL)applicationShouldOpenUntitledFile:(NSApplication *)sender
{
return NO;
}

Call the new/open action when the registration window is closed.

