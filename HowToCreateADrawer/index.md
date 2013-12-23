---
layout: page
title: HowToCreateADrawer
---



This is VERY easy.  First, go into Interface Builder.  Go to the "Cocoa-" panel and choose "Windows."  Drag a drawer object into the MainMenu.nib window.  Go to the "Cocoa-Containter-Views" panel and drag a CustomView to the MainMenu.nib window.  Control-drag from the NSDrawer to the View.  Select ContentView in the info panel and hit the connect button.  Control-drag from the NSDrawer to the window you want the drawer to be under.  Select ParentWindow in the info panel and hit the connect button.

Create a button on the window that will open the drawer.  Control drag from the button to the NSDrawer icon on the MainMenu.nib window.  Select target -> toggle in the info panel and hit connect.

Pressing the button on the window will toggle the drawer on and off.  Try it out (see HowToTestTheInterface).

You can add whatever you want to the drawer by adding it to the View you made.  Treat it like another window.

Of course, you can toggle the drawer programmatically. Simply place an outlet in the class you desire and call [myDrawer toggle:self]; or if you really need specific control, [myDrawer close]; and [myDrawer open];

Congratulations.

Learn more about drawers here - http://developer.apple.com/techpubs/macosx/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSDrawer.html

Go back to HowToProgramInOSX

You may also want to read DrawerAbuse

----

A little advice on using general variable names (as I have changed above): Don't use the name of the class (NSDrawer changed above) to be a placeholder for an instance variable. Instead, use a variable name that looks general and follows the standard conventions for variable names (myDrawer above). Otherwise, it looks like you're using a class method. Thanks!

*Maybe MikeTrent can link us to the page telling us not to use "my" either! Mike?*

StopUsingMyInVariableNames

----

This works great, and I figured that out without reading this... However, I'm having a problem in that the initial drawer that drops down is sized in a weird way (too long, and the content only takes up the lower portion of it)... However, if you click on the resizer, it snaps back to a fixed "maximum" size much smaller, and won't let you make it bigger.  Is there way to have a view in a drawer like this grow "infinitely" in one direction?

*This happens to me occasionally too. I'm not sure why, but I do know that drawers are constrained to the size of their parent window - you can size the drawer larger, but when you try to resize it, it "remembers" it's window's size and snaps back. Does this sound like your situation?*

"I've figured out why that is happening. It is a bug in Xcode that is cured if you quit and restart Xcode."
----

Here's another question.:


I want to open the drawer from the main menu. I can't seem to ctrl-drag from a menuitem onto the nsdrawer..

*is the drawer in the same nib as the menu? you should be able to ctrl-drag in that case. if this is between different nibs, no, you can't do that. use FirstResponder*

----

Can a sheet display a drawer?

