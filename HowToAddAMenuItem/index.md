---
layout: page
title: HowToAddAMenuItem
---



All you do to change the menus and menu items for your program is go into Interface Builder and play around with the "MainMenu.nib - Main Menu" window.  It is the smallest window of the four or so windows that are present.  It looks like a menu bar.

You can change it to your heart's content.  You can even use control drag to connect them to action methods in any of your classes.

You can also dynamically create menus, for instance, you may want to change the Window menu on the fly to reflect what windows you have open and what you can do to them. For more information seek NSMenu and NSMenuItem.

----

Step by step instructions for a document based application:

*
Add an action method to your NSDocument subclass header.  Most often, this is called MyDocument.h.  The method declaration might look like 
    
- (IBAction)export:(id)sender;

*
Add the method to MyDocument.m:
    
- (IBAction)export:(id)sender{
	NSLog(@"export");
}

*
Open MainMenu.nib
*
Duplicate an existing item (or use copy and paste)
*
Rename it.  In this case, call it "Export..."
*
Select "First Responder" in the Classes tab
*
Select "Add action to First Responder" in the classes menu
*
Give it a name matching what you called the IBACTION method in your NSDocument subclass.  In this case, "export:"
*
Control drag from the new menu item to FirstResponder, and connect to export:
*
Run the program, and note that selecting the menu item prints "export" in the run log

----

Go back to HowToProgramInOSX

