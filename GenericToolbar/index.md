---
layout: page
title: GenericToolbar
---

Created by JediKnil
----
*Quick summary: GenericToolbar contains NSCoding-compliant subclasses of NSToolbar and NSToolbarItem for use in Interface Builder, as well as a palette (also for use in Interface Builder).  http://goo.gl/Cx9sQAlternatives include UliKusterer's UKToolbarFactory...and just creating toolbars by hand (read: programmatically). Current Version == 1.0*

Download/Info page: http://www.belkadan.com/generictoolbar/

Questions, comments, bugs, etc...either e-mail me or post them here.
----
**Updates**

*22 November 2005 - Version 1.0 released (succeeding the not-so-complete version 0.9)

----
----
Finally, a toolbar that can be used in Interface Builder! GenericToolbar is easy to use, convenient, and totally free, which is a great combination for any developer. Coolest of all, GenericToolbar supports Interface Builder's "Test Interface" mode! You can check out the Example project (included in the download) for a simple demonstration. To use GenericToolbar in your own project:

1. Double-click the G<nowiki/>enericToolbar.palette icon to load the palette into Interface Builder. This loads both a template palette and the classes GenericToolbar and GenericToolbarItem. To ensure a unique class name, both classes are actually prefixed with C<nowiki/>omBelkadanGenerictoolbar_. This will show up in Interface Builder, but thanks to #define, it won't in your code (if you #import the header files). You will only have to do this step once unless you later remove the GenericToolbar palette from IB.

2. Drag the icon for a GenericToolbar instance into your nib's document window (NOT the window you want to attach the toolbar to).

3. Connect your instance of NSWindow to the instance of GenericToolbar ("toolbar" outlet).

4. Set up your toolbar. You can drag new items into the toolbar from the GenericToolbar palette. (The item with dashed lines represents a customizable items, though you can change all of them if you want) Toolbar item actions are set using the usual method of connections: Control-click and drag.

5. To use one of Apple's default toolbar items, just enter the identifier, leaving all other fields blank. This identifier can be any of the following:

* NSToolbarSeparatorItemIdentifier
* NSToolbarSpaceItemIdentifier
* NSToolbarFlexibleSpaceItemIdentifier
* NSToolbarShowColorsItemIdentifier
* NSToolbarShowFontsItemIdentifier
* NSToolbarCustomizeToolbarItemIdentifier
* NSToolbarPrintItemIdentifier


6. To make a custom view item, connect the item to the view of your choice. Set the size of the item as well to avoid a size of 0 by 0 pixels. There is an option that allows you to automatically resize to match the view: -1 by -1 pixels. DON'T FORGET THIS STEP!

7. To set the default or selectable items in the toolbar, just drag the item from the "All Items" box down to the bottom box. To delete an item, drag it out of the box. To move an item, drag it to its new position. Make sure the mouse is not outside the box when you let go, or the item will be deleted. However, GenericToolbar has (almost) full undo support, so just press Command-Z to bring your item back.

8. You have three options for adding GenericToolbar to your project. This IS necessary, even if you are not going to use the classes outside of the nib.

A) Add the eight source code files to your project (in the Source Code folder)
- GenericToolbar.h/.m
- GenericToolbarItem.h/.m
- GenericToolbarDataSource.h/.m
- GenericToolbarNibConnector.h/.m

B) Add the GenericToolbar framework to your project. This must be recognized as a framework, but also copied into your final product. To do this, add a Copy Files build phase to your target, with the destination set to Frameworks. Then drag the framework into the Copy Files build phase. Make sure it still shows up in the Link phase. There is one advantage of this: localized error messages for toolbar decoding. At least, there would be if I knew any other languages to write error messages in.

C) Add the GenericToolbar static library to your project (libGenericToolbar.a).

9. For the GenericToolbar API, look in the docs folder. Most users won't need to touch this.

10. Build and run!
----

Well, it doesn't work for me. I use some buttn and connect it to an action, and the action fires when hitting the button. I connect same action with one of the toolbar items, but it never fires, delegate and all else hooked up.
----
Great...I thought I fixed that. Can you send me your nib file and tell me what version of OS X you're using? jediknil AT yahoo DOT com.
----

I clicked on the example link at the top, and it doesn't work for me. 3/26/06 12:08am CST
Let us know if it's temporary, or if it's an old link, etc.
----
Sorry, old link. Fixed

----
I import the source to my project, get it correctly working with my NIB (it shows when I build).  Now I'm attempting to add buttons to it.  I drag the dotted line item place holder, drag a image onto that and it shows in the toolbar.  If I ctrl-drag to a drawer for example to toggle it and connect it, I cannot click the item in the toolbar in order to toggle my drawer.  The image remains grayed.  Am I missing something?  OSX 10.4.5.  XCode 2.2.
----
I don't see any problem...but unfortunately I don't have 10.4.5 yet. Maybe Apple changed something. Sorry... --JediKnil
----
Server seems to be dead (20060410)
----
It's not...though I just fixed the link as of July 31, 2006. --JediKnil
----
Hi, my custom items remain selected (on a dark background) after I clicked them. How can I get rid of that. many thanks for that wonderfull work. (20061029)
----
Hi, 

I just went to your website & found the 1.1 version, and tried to upgrade from my 1.0 version. When I double-click     GenericToolbar.palette, I get the message "The document "    GenericToolbar.palette" could not be opened. I then tried going back and using the 1.0 palette, and I can open Nib files that have Generic Toolbar, but I don't have the toolbar itself any more. I'm using OS X, version 10.4.10, XCode 2.4.1, and IB 2.5.4.

Thanks!
----
This should have been added to IB by default a long time ago. What a time saver! Well done.
----
You neednt be double clicking palette packages, if you want to upgrade a palette you should just replace the old version of the palette with the new version of the palette. IB keeps a record of where you stored the palette so it should just switch over smoothly, unless the name or identifier has changed. The other way is to use the palatte manager accessible via Tools > Palettes > Palette Preferences... and remove the old version and add the new version. Don't try to open NIB's that use a custom palette without IB being able to locate it, you will just get error messages until IB gives up.

