---
layout: page
title: NSMenu
---

Apple's documentation: http://developer.apple.com/techpubs/macosx/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSMenu.html

----

NSMenu on OS X uses the Carbon menu manager, but only exposes a fraction of its capabilities.  If you want to do the other fun stuff that the menu manager provides, it's time for some

**Undocumented Goodness!**


AppKit declares a _NSGetCarbonMenu function that returns a MenuRef. The function's declaration is:

    MenuRef _NSGetCarbonMenu(NSMenu* aMenu);

Note that Carbon menu items within a menu are counted from 1, but Cocoa menu items are counted from 0.

Note also that this function is only effective on menus which have been added to the menu bar or popped up.  If you have a popup menu which you need to manipulate, try adding it to the menu bar temporarily.

Finally, I learned of this function from a poster on Apple's cocoa-dev mailing list, who learned about it from OmniGroup's OAApplication class.

If you have to use this API, please file a bug asking for additions to NSMenu/NSMenuItem so more of the menu manager is exposed in Cocoa.  The more people that file bugs, the better chance that such features will be added.

----

I have been trying how to figure out if a user clicks on a menu item in the menu bar. From what I have found, it seems that I'll have to get it via Carbon. I have been googling for a couple of hours, and haven't found much. I think that MenuChoice is the way to go, but I have no idea how to access this from Cocoa. I -very- little Carbon experience, and dont know the first thing about using code like that. If anyone can give me a shove in the right direction, it would be greatly appreciated. Thanks,

-- Jacob

*What are you trying to do?*

I'm trying to figure out when someone is clicking (and holding) on a main menu item (ie. when someone clicks on the "File" item).

*Yes, I understood that much. My question is, what is your end goal? What are you trying to accomplish? I ask this because there may be a better or simpler way of doing it, but we can't tell you what that way might be if we don't know what "it" is.*

----
hi,
i use a number of NSPopupButtons to invoke several menu's, but the key-equivalant of the menu-items in those menu's don't work.
they only work if a menu is opened/shown by clicking the popupButton.

how cab i have them respond to key-equivalants even when they're not shown?

thanks
arri
----
I have a special case where I don't want the Menu to close after the user has clicked on an item, but I don't see how I could implement that. Does anyone have an idea?

----

Explain your "special case" in more detail. It sounds like you're using the wrong type of user interface for the task at hand. usually users react badly to odd behaviour like that and will assume it's a bug, so if there's another way, do it another way. I know this sounds high-handed and I get pissed off sometimes when given the same advice in special circumstances, so that's why I suggest you explain your special case some more. It might be valid, it just seems unlikely. --GC 
----
Sure, here we go:
I have a menu with the file system hierarchy. That's the whole app, there's nothing more to it. Now, when the user clicks on it, it should close and open up the folder/file in finder/the according app. But, if he holds down option and clicks on it, I want the QuickLook window to show, without the menu closing... Or, it should open again after the user disposes of the quicklook window - something like that.
----
And where does one click to get this menu?
----
A custom floating window.

----
Well, I'm not entirely sure it would be a good idea to attempt to replace the click-to-close paradigm.  Besides, it certainly won't be easy, considering that     NSMenu relies on the Carbon Menu Manager.  Plus, menus have two modes: click to open, click to close; and click-and-hold to open, release to close.  You might serve yourself, and your users, much better if you don't use the standard NSMenu for this task. --K

----

A custom view with cells may be your best bet, rather than a true menu. You're going to go through a lot of trouble customizing menu behaviour that could change in the next OS release anyway.

----

If you have a floating window why not just have it display a hierarchical list of the file structure? Hmmm, you could call it the Fidner, or something. ;-)

