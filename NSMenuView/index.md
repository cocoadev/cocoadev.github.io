---
layout: page
title: NSMenuView
---

Back in the old days, subclassing this view would let you customize the look of menus.  It's now deprecated functionality, and actually doesn't do anything anymore.  To do any custom menu drawing stuff you have to dip down into Carbon (or find a pre-built wrapper class out on the net somewhere).

In Rhapsody and Mac OS X DP1 and DP2 the stuff that managed the menu bar and menus was written in Cocoa. Since the switch to the Aqua interface in DP3, however, Apple chucked most of the Cocoa menu implementation in favor of the Carbon Menu Manager, probably so as to allow for Carbon and Classic to use it. (Since Classic itself is Carbon). Of course, in doing this, we lost a lot of cool stuff like tear off menus. NSMenuView and NSMenuItemCell are casualties of this switch. It is interesting to note that the AppKit documentation does list these two classes as deprecated, but does not, however, list them as 'to be removed' from the Application Kit as it does for the NSMenuItem protocol. It makes you wonder if Apple is holding this in reserve in case it wants to reimplement some functionality like the tear-off menus. Hmm...
----
[Discussion of Cocoa features lost in transition from Openstep/Rhapsody to Cocoa moved to OpenStep page]

----

So I wonder what's the best way of going about making a custom menu? I want a menu which displays a series of line dash styles, so I need a way to draw those lines as menu items. If there's no support in Cocoa and only the Carbon way officially available, I wonder if it's worth skipping that altogether and simply pop up a borderless window and handle it all there? Anyone any experience of doing this? --GrahamCox

For line styles it is probably just as easy to use images, as in Keynote and Pages for example. They are just pre-made images that are set probably in awakeFromNib with a blank title for the menu item. You could easily create these in Photoshop if there are a large number of them. --LoganCollins

