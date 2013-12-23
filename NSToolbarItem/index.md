---
layout: page
title: NSToolbarItem
---



The descriptions down here are NOT official. They're straight from the headers and may change. 

We also have some NSToolbarItemSampleCode 

----

Question: how can I create a toolbar item with a custom view...that also allows duplicates? Is this even possible? If I just subclass NSToolbarItem, set the view, and return from allowsDuplicatesInToolbar, then the item works fine, the view works fine (an NSButton in this case), and I can't drag another copy into the toolbar. Is this just something that can't be done? --JediKnil

----

Initialize the toolbar item with an identifier which is a development language string used by the toolbar and its delegate for identification purposes.
----
[http://www.doxapest.co.id/index.php/pest-control-dan-anti-rayap Pest Control] & [http://www.doxapest.co.id/index.php/pest-control-dan-anti-rayap Anti Rayap], [http://vamostech.com/gps-tracking GPS Tracking], [http://www.propertykita.com/rumah.html Rumah Dijual], [http://subconscious-therapy.com/hipnoterapi-surabaya/ Hipnoterapi Surabaya]
----

----
----
**Setters and Getters**


Use this to set the item's label that appears in the toolbar.  The implication here is that the toolbar will draw the label for the item, and a redraw is triggered by this method.  The toolbar is in charge of the label area.  It is fine for an item to have no toolbar label.  Also, developers should make sure the length of the label is appropriate and not too long.
----

Use this to set the item's label that appears when the item is in the customization palette.  All Items must have a palette label, and for most things it is reasonable to set them to the same string as the label used in the toolbar.
----

Use this to set a tooltip to be used when the item is displayed in the toolbar.  (forwards to -view if it responds)
----

The menu form of a toolbar item's purpose is twofold.  First, when the window is too small to display an item, it will be clipped but remain accessible from a "clipped items" menu containing the menu item returned here.  Second, in text only mode, the menu returned will be used to create the displayed items.  Singleton menu items will be clickable, while submenu items will be represented as a pull down.  For instance, say you want a button that allows you to switch between modes A, B, and C.  You could represent this as a menu by :  a menu item "mode" with three submenu items "A", "B", and "C".   By default, this method returns a singleton menu item with item label as the title.  For standard items, the target, action is set.
----

----
**Validation of the items**


Typically you should not invoke this method.  This method is called by its toolbar during validation.  Standard items validate themselves by sending the validateToolbarItem: validate message to the current validator.  Since items with custom views don't always have meaningful target/actions, they do nothing.  So for your custom items it may be useful to override this method and invent your own validation.
----
----
**Controlling Duplicates In The Toolbar**

    - (BOOL)allowsDuplicatesInToolbar;
Return YES to allow dragging duplicate items into the toolbar.  By default, if an item with the same identifier is already in the toolbar, dragging in will act as a move of this item.  However, for instance, the separator item drags in as a duplicate always.
----
----
**interface NSObject (NSToolbarItemValidation)**


NSToolbarItemValidation extends the standard validation idea by introducing this new method which is sent to validators for each visible standard NSToolbarItem with a valid target/action pair. 
Note: This message is sent from NSToolbarItem's validate method, howevever validate will not send this message for items that have custom views. Moreover, it is sent to the item's target, NOT to the toolbar delegate.
----
----
**Standard Toolbar Item Identifiers:**


----

*Please do not remove the contents of an entire page to post a new question*

How do I disable the items in NSToolbar during runtime? 

----
Implement this method:

----
It should be noted that, at least in my experience, the above method needs to be implemented in the NSToolbarItem TARGET, not in the Toolbar Controller itself.

----
Yes, of course, this is exactly like Cocoa's other methods.

----

Is it possible to prevent the user from moving particular items in a toolbar? E.g. in X-code with the All-in-One layout set the page switching item can�t be (re)moved. I found the private method -NSToolbarItem _setIsUserRemovable: but passing NO still allows the user to move the item around in the toolbar, just not remove it.


----

Apparently when you open a sheet on your window, it will disable all the buttons on your toolbar. Then after the sheet ends, it will restore the state of the buttons afterwards. The trouble is, if you change the enabled state of the buttons in your alertDidEnd:returnCode:contextInfo: method, your change will be lost. Even worse, if you are attempting to bind the enabled state of the button through a binding, this restore process apparently ignores your binding and stomps all over it. The only solution I know of is to do an ugly hack using performSelector: to reset your desired state of the button on the next iteration through the runloop. Unfortunately there is enough brain-damage in Cocoa toolbars that the enabled bindings are close to useless.

