---
layout: page
title: BindToExternalFile
---


Hello all. Here's my scenario: I have a NSArrayController, in a MyDocument.nib, which I'd like to bind from a Main Menu Item (which is, obviously, located in MainMenu.nib), to make the item to be enabled or not according to the NSArrayController's canRemove key. Could someone please tell me how can I do such a thing?

----

Menu items are generally controlled by a special method somewhere in the responder chain.  Presumably, your MyDocument class can implement the following:

    
- (BOOL)validateUserInterfaceItem:(id <NSValidatedUserInterfaceItem>)anItem
{
	SEL action = [anItem action];
	if (action == @selector(remove:))
		return [mainController instance] arrayController] canRemove];
	return [[self nextResponder] validateUserInterfaceItem:anItem]; // If it isn't handled here, bounce it to the next responder
}


I think this should work although mileage may vary.  I have not tested this.

-G

----
Let me add something i missed in the first message: my goal is to replicate a button's behavior in the main menu as well. Since I used a binding to enable/disable the button, according to [[NSArrayController canRemove, I thought I had to use the same mechanism.

----
Nope, validateUserInterfaceItem is probably the cleanest way to do it, but you could alternatively expose the object you're binding with in MainMenu.nib as an outlet of the delegate of NSApp (aka File's Owner in MainMenu.nib).  Seems more effort than it's worth though, as Cocoa will automatically ask the key window's delegate (your document) about the interface item anyway, rather than requiring you to write the code to expose the current document's version of the array controller as an object in a delegate of NSApp.

