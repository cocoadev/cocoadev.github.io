---
layout: page
title: MenusAndDocuments
---



This page is also linked *from* CommonProblem

see also MakingNibsTalkToEachOther

see also FirstResponder

see also ResponderChain

I have one nib with a     NSDocument (Cocoa Document-based application default one), the nib is called     M**'yDocument.nib and the other nib,     Properties.nib, contains a panel and is connected to a     NSWindowController subclass.

Commands such as those in the Application menu need to be connected to the File's Owner of     M**'ainMenu.nib. Others, however, need to be connected to objects in the document nib file.

----

Add your document's actions to the M**'ainMenu.nib's FirstResponder and connect the menus to those. At runtime your document class' implementation will be executed (assuming nothing ahead of it in the Responder Chain implements those methods)

To do this, first create the action you want to be called in the NSDocument subclass in M**'yDocument.nib. Then double click the FirstResponder in Properties.nib. Add the action with the same name to the First Responder. When calling the First Responder's action, it will go down the ResponderChain and call the first object that replies to that method. In other words, the currently active document. Note: be careful using common method names for this - you don't want another object to respond to the method before it gets to the document.

This might help too: http://www.randomsequence.com/Tips%20&%20Tricks/Cocoa/First%20Responder.php
(Missing see the Wayback: http://web.archive.org/web/20051002030453/http://www.randomsequence.com/Tips+&+Tricks/Cocoa/First+Responder.php )

Adding the action to the First Responder in M**'yDocument.nib is unnecessary.
This is because M**'yDocument.nib does not need to know about it (no connections are made to it there, the connections are made in MainMenu.nib).
What is important, however, is that the M**'yDocument class responds to this method (in the code). Basically, if some outlet or action is not connected in InterfaceBuilder, you do not need it.

----

I know how to access the CurrentDocument of my app from MainMenu.nib using sharedDocumentController, ie:

    
myApp = [[NSDocumentController sharedDocumentController] currentDocument];


But I'd like to know how to access the document's window itself and its methods. Anybody know what to do here? Thanks!

----

In your nib you need to double-click the FirstResponder icon and add the method signature you want to message. For instance, if your NSDocument subclass has a custom method you defined called -(void)sendEmailToSelectedContact:(id)sender you would add the method signature as "sendEmailToSelectedContact:" (note the colon on the end, leave out the quotes). Create a button and you should then be able to connect it to the FirstResponder's new sendEmailToSelectedContact: method.

----

If you need to access an actual nib object, you can just set an accessor method on the nib's owner (usually either your NSDocument or NSWindowController subclass). For instance, if you wanted to access an NSArrayController you instantiated in the nib, you could do:
    
NSArrayController *foreignArrayController = currentDocument mainWindowController] mainArrayController];

This calls two accessors, one in your document, and one in your window controller, which would be the nib's owner, and have the connection to the arraycontroller in the nib. This doesn't really strike me as a good design pattern though, and I would personally avoid it when possible.

----

From Steven Noyes (via macosx-dev mailing list):

Re: How to connect an object to another object in a separate [[NibFile

Most of it boils down to the concept of the FirstResponder.  In IB, it  is designated by the simple icon of the number one.  This is the tie in to your menus  and many other things.

As an example.

Your menu might have a selection that prints a picture.  The problem is 
the controller to handle that is some other NIB.  No problem.  In your main NIB you add an action to the FirstResponder that matches the method you want called to handle the print picture action. The menu is then connected to the FirstResponder.

You then add a matching method in your print picture controller and make 
sure it is in the ResponderChain.

This will do a couple of things.  If your "print picture" method is in 
the ResponderChain, Cocoa will automatically allow that menu item to be selected.  If it is not, it will be greyed out.  It also means is that EVERY selectable item in a menu has some method that will respond to it.  This is cool.

----

So what is the best way to manage "global" windows in a document-based application? For example, a registration window. Is the best way to override the NSDocumentController? It would need to hook into NSApp delegate methods. Or what about "manually" updating menu items separate from whether the FirstResponder responds to its selector?

----
You seem to be overthinking the problem. The regular FirstResponder action chain will work fine with these windows due to how Cocoa differentiates main and key windows. There's no need to hook into NSApp delegate methods, the notifications are sufficient. You can just subclass NSWindowController to display the window, listen for notifications so you can refresh your contents based on the front document, and then have your NSApp delegate instantiate your window controller as needed.

----

Also, don't forget that if you have menu/button commands that don't need to go to "whatever's current", but instead to some specific window, you can simply hook them directly to that window's controller, if it's part of MainMenu.nib. Menu commands that open, close or toggle such global windows I find are usually best directed at the application's delegate directly or at MainMenu.nib's "File's Owner" (the App). The first responder method is ONLY needed for commands that are context sensitive and/or target a controller that is in a different nib. --GC.

