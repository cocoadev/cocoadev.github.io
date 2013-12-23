---
layout: page
title: DynamicMenuForNSPopUpButton
---

I have an NSPopupButton in an NSToolbar which allows the user to select one of several items. Items can be added, removed and renamed through various means.

Can I delay the creation of the submenu (for the NSPopupButton) until it actually opens, or will I have to add some hooks for every function that can insert, remove or rename an item, and let this hook trigger a notify or similar which instructs me to recreate the menu?

----

The NSPopUpButton sends a notification, NSPopUpButtonWillPopUpNotification, right before showing the menu.  You can listen for that as a cue to set up your menu. -- Bo

----

It seems that setMenu: invoked from this notify (on the popup) will hang my application -- but removeAllItems followed by addItemsWithTitles work, then I'll just have to query the menu afterwards and set-up menu item tags.

----

If you've got a fairly complicated setup routine, you're probably better off just grabbing the menu (with -menu) immediately and making the changes directly to it.  It's less fiddly in general to set up each item exactly how you want it and then add it, than to add all the items and then go back over them making changes.  -- Bo

----

I have a subclass of NSPopUpButton which uses a subclass of NSPopUpButtonCell, but the pull-down menu doesn't always come up when the user clicks the pop-up button. Here is the code in the subclass of NSPopUpButton (PFPopUpButton) which sets its cell as the subclass of NSPopUpButtonCell (PFPopUpButtonCell):

    
- (void)awakeFromNib
{
    PFPopUpButtonCell * popUpButtonCell = [[PFPopUpButtonCell alloc] initTextCell:[self title] pullsDown:YES];
    [popUpButtonCell setArrowPosition:NSPopUpNoArrow];
    [popUpButtonCell setShowsBorderOnlyWhileMouseInside:YES];
    [popUpButtonCell setBordered:NO];
    [popUpButtonCell setFont:[NSFont fontWithName:@"Helvetica" size:10]];
    [popUpButtonCell setTextView:textView];	

    [self setCell:popUpButtonCell];	
}


If I delete that last line, the menu works fine, which means there's something wrong with either the cell or the way I'm setting the cell. Am I doing this right, or is there a better way to go?

----

Try     [popUpButtonCell setMenu:[self menu]];

--JediKnil

----

I have an NSMenu populated at runtime from the document data. (For an oversimplified example, let's call it "colorMenu" and say it ends up having { "Red","Green","Blue" } as its menu items.) 

I have a number of different settings all based on this data. (e.g., "background color", "foreground color", and "border color".) I've currently implemented this as a NSMatrix of NSPopUpButtonCells.

The user can edit this data while the program is running. (For example, changing "Green" to "Purple".) Each of the setting buttons needs to reflect this change when it happens.

I've tried two approaches:


* Create the prototype cell for the NSMatrix and call setMenu:colorMenu on it before populating the matrix with renewRows:r Columns:c. This apparently creates a new copy of the menu for each cell in the matrix, so changes to any one menu's menu items are not reflected in any of the others. This also creates a noticeable delay when the matrix is first created.
* Populate the NSMatrix first, then call setMenu:colorMenu on each cell individually. With this approach, the cells do all share the same NSMenu, and calling selectItemAtIndex to set each cell's selection programmatically works just fine. (I can programmatically set background color to Red and foreground color to Green without problems.) However, as soon as the *user* makes a selection in any cell, all the other cells change to reflect the same selection. (Suddenly everything is Blue, just because I changed the background color to Blue.)


Do I need to subclass NSPopUpButtonCell and/or NSMenu? If so, which methods would I override?

How about NSComboBox[Cell], maybe? Since I wouldn't be allowing any selections that weren't part of the existing list, it would be a rather nonstandard use for that class...)

----

Don't share the menu. Things like that don't like to be shared. Use a different menu for every cell, and just modify them all every time a change is made.

----

I may have a dozen or more of these cells by time this project is done. That'll be an absolute pain to do, plus there's the lag time involved in creating that many new menus when the window opens.

----

I don't see why it would be a pain or why it would be slow. Computers exist to take care of boring repetitive tasks for you. Adding a new item to one menu should be no easier than adding it to a thousand. As far as speed goes, have you actually tested it? I have trouble believing that creating a dozen menus will take any noticeable amount of time even on the slowest machine that will actually run OS X.

If you still don't like that, and you can require 10.3, use NSMenu's new delegate functionality to only update the menu when it's opened. That way you're not updating them all at once, things are nice and fast, and you're being properly lazy.

----

The example I gave here is greatly oversimplified - some of these menus have 200+ entries, and I'm making as many as a dozen copies of some menus. I know that's far beyond the usual recommended use of a pop-up button, but space is too tight for me to use an equal number of scrolling lists instead.

I'm able to get a slight improvement in speed by using a combination of the above methods - I go ahead and create the matrix of NSPopUpButtonCells in InterfaceBuilder, then at runtime I iterate through [menuMatrix cells] and call [cell setMenu:[myMenu copy]] on each one. Since the menu is being duplicated, but the cell is already present, it's slightly faster. The delay is still noticeable, but it's only about a half-second for the entire setup, and since my computer is getting pretty old at this point (4+ years), that's probably fast enough for now. (Maybe in the future I'll try using a menu delegate, if performance becomes a major issue.) 

----

I have an NSPopUpButton (pull-down), and I want it to have a static title which isn't the name of a menu item in its associated NSMenu. I know how to do this by using addItemsWithTitles: to populate the NSMenu, but I want to set the menu in IB. However, when I set the menu items in IB, the title of the NSPopUpButton is added as a menu item when I run the program, though it doesn't show up in IB.

----

You could create a 'dummy' button and attach a menu to it. This is not the expected behavior for a Cocoa app. I would recommend finding another way of doing this. Perhaps like the "Action Button" found in apps like Mail (the bottom button with the gear icon). I would be quite confused if a popup button, which works the same in every app suddenly worked differently in yours; I'd think something was wrong (my selection wasn't 'taking') and likely assume it was a very obvious bug you'd missed. I cannot suggest strongly enough that you do not modify the behavior of basic controls in this way.

----

It is like the "action button" in Mail, only it has some text as a title, rather than an image. Does the Action Button use a dummy button with a menu attached to it?

To be more specific, I am trying to get the behavior of the NSPopUpButton in an entry in Address Book.

----

The pull-down variant of NSPopUpButton works exactly like the OP describes. Like the "Action Button" but with a title rather than an icon. The up/down arrows on the right side are replaced with a single down arrow to tell the user what kind of control it is.

NSPopUpButton uses the first item of its menu as its static title. That's how it works, period. It doesn't appear in the menu when the NSPopUpButton is clicked, though.

What pop-up buttons in Address Book? The labels for the various fields? Those are PopUp-style controls, not PullDown-style.

----

"It doesn't appear in the menu when the NSPopUpButton is clicked, though."

I'm using a custom NSPopUpButton cell, so I have to call     [popUpButtonCell setMenu:[self menu]]; (popUpButtonCell is the NSPopUpButton cell) in the NSPopUpButton awakeFromNib: method, and then the title of the pop-up button shows up in the menu. Why does it do this, and how can I get around it?

----

I'm trying to create an NSPopUpButton that allows the items to have submenus, and I'd like to delay-load the submenus as much as possible, by setting the submenu's delegate and handling     -menuNeedsUpdate, for performance reasons. But there's a catch -- it doesn't work with all NSControlSize values.  Basically, if I populate the entire menu tree, including submenus, at the time     NSPopUpButton(Cell)WillPopUpNotification fires, everything works properly, and the submenus take on font sizes based on the NSControlSize of the popup button.

But, if I instead put empty NSMenus as submenus of the items in the popup button's item array, then the "root" menu items have the correct font size, but the submenu items, when populated with a later call to     -menuNeedsUpdate, always have the large font, regardless of whether the popup button has NSRegularControlSize, NSSmallControlSize, or NSMiniControlSize. It would appear that there's some "magic" going on inside Cocoa that causes it to automatically alter the font size of the menu items, but only in the context of certain events, and I can't figure out what that "magic" is.

I've tried     -setAttributedTitle on the menu items with     +systemFontOfSize to get the correct font, but the titles don't line up properly, and it looks amateurish.  Does anyone else have any ideas?

