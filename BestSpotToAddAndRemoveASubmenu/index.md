---
layout: page
title: BestSpotToAddAndRemoveASubmenu
---



I have a menu item "Import" in the main menu.

I want to add a submenu to that item but the number of items and title of the submenu depends on the active window/panel.

Adding a submenu and removing isn't the problem but I wonder how and where this is best handled?

----
Please don't do this.  If it is not explicitly against the HIG, it should be.  Users need to be able to learn the structure of the menus.  If the structure (not just validation) changes based on the current selection, users will never know what is or should be in a menu.  What you want to do violates the principle of "least surprise".  

Why not just enable and disable the menu items that apply in different situations and leave them in the menu?  It will even work automatically with menuValidation. (No code) That way users who look at the menu will know the item is sometimes enabled...it is sometimes an option even if it isn't now.

Leaving the structure of the menus unchanged makes it easier to document the menus in your manual.  How many pictures of the different permutations of menu contents do you want in your manual ? Leaving the structure of the menus unchanged makes loacalization of menus easier.  There is only one place to do translations etc.

But... to answer the question: 
- (BOOL)validateMenuItem:(id <NSMenuItem>)menuItem

-or-

 NSMenu's delegate's - (void)menuNeedsUpdate:(NSMenu *)menu

Called when a menu is about to be displayed at the start of a tracking session so the delegate can modify the menu.

- (void)menuNeedsUpdate:(NSMenu *)menu

Parameters
menu  The menu object that is about to be displayed.

Discussion
You can change the menu by adding, removing or modifying menu items. Be sure to set the proper enable state for any new menu items. If populating the menu will take a long time, implement numberOfItemsInMenu: and menu:updateItem:atIndex:shouldCancel: instead.

Availability
Available in Mac OS X v10.3 and later.
See Also
� setDelegate:

----
Yes, I could do what you suggest. No doubt this is the most simple solution as long as there are only limited menu items.
Thanks for your other suggestions!

