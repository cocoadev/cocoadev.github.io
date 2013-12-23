---
layout: page
title: HavingTroubleCallingNewDocument
---

This is bothering me because its too simple...
Ok I have this code in **MyDocument.h**
    
-(void)new:(id)sender
{
	[NSApp newDocument];
}

And I get this error during runtime...
    
[NSApplication newDocument]: selector not recognized

Can anyone point out any mistakes?

*The NSApplication class doesn't have a method named -newDocument. Are you sure you don't want to do something like     [[NSDocumentController sharedDocumentController] newDocument:nil]. But this is the default action of the "New" menu item anyway...so what exactly are you doing there?*
----
I'm trying to have an NSToolbarItem that creates a new document.

*add a selector to your toolbar's target and hook up your toolbar item to it.*

    -(IBAction)createNewDocument:(id)sender {[[NSDocumentController sharedDocumentController] newDocument:nil];}
----
How would I initiate a save?

----

*Even better than ceating a -createNewDocument: method, set the toolbar item's action to @selector(newDocument:) and target to nil (first responder). To initiate a save, do the same, except change the action. You can find out the appropriate actions by opening up the MainMenu in IB and using the inspector to check the actions. That's what makes Cocoa great: the responder chain means that you don't have to create these sort of redundant methods.*

