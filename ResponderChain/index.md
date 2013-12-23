---
layout: page
title: ResponderChain
---



A ResponderChain is an hierarchy of objects that have the opportunity to respond to events received.

The first object in the ResponderChain is called the FirstResponder. To send an action message to the first responder, make the target nil (see NilTargetedAction). This is exactly what InterfaceBuilder does.

All objects in the responder chain except delegates need to inherit from NSResponder (q.v.).

----

I am incorporating the comments below (to the double line) in writing what will hopefully be the definitive responder chain.

*Key window
*FirstResponder, e.g. a text field
*Other NSResponder<nowiki/>s containing the FirstResponder up to (and including) the content view
*NSWindow
*NSWindow delegate
*NSWindowController (if present)
*NSDocument (via NSWindowController)
*Main window
*FirstResponder, e.g. a text field
*Other NSResponder**'s containing the FirstResponder up to (and including) the content view
*NSWindow
*NSWindow delegate
*NSWindowController (if present)
*NSDocument (via NSWindowController)
*NSApplication
*NSApplication delegate
*NSDocumentController

----

From Steven Noyes (via macosx-dev mailing list)

The responder chain??? That is a tricky question.  At all times there is 
a responder chain that is defined to handle events that are wired to the FirstResponder.  It goes kinda like this. I might have it slightly off, but I am sure I will be corrected.

1.  The current object of focus.  This might be a text field of a switch.

2.  The parent view.   May be another view or a window.

3.  This FilesOwner if I am not mistaken.

4.  The application.  This includes the menus and other NSApplication 
stuff.

There might be multiple levels and each item might have been subclasses defined.  Likewise, this makes it possible to have object message objects that are higher in the responder chain but not in the current NIB.

----

The window's delegate gets in there at around 2.5, as well.

----

Apparently the window's window controller too - [http://www.cocoabuilder.com/archive/message/cocoa/2004/10/8/119067]

----

According to Apple:

It begins with the first responder in the key window and follows nextResponder links up the responder chain to the NSWindow�s content view.

It tries the NSWindow object and then the NSWindow�s delegate.

If the main window is different from the key window, it then starts over with the first responder in the main window and works its way up the main window�s responder chain to the NSWindow object and its delegate.

Next, the NSApplication object tries to respond itself. If it can�t respond, it tries its own delegate. NSApp and its delegate are the receivers of last resort.

[http://developer.apple.com/documentation/Cocoa/Conceptual/ActionMessages/index.html]

This is also how Aaron Hillegass describes it in BookCocoaProgMacOSX. It doesn't look like the window controller is involved. But the window controller is often the delegate of the window, which does put it in the search list.

----

That's what I thought too, but the previous message in that ml thread was asking precisely that. I guess I'll check into further when I get to a Mac. 

----
----

I'm trying to use tab controls in my application and have been looking around the Internet for any source code of a sample app that shows how to use NSTabView. The whole reading which tab you are in (identifier) and getiing information from the items found therein, like textfields, checkboxes, radio buttons, etc. Has anyone come across something like this? Apple did have a sample code called SimpleCocoaApp or something like it but it didn't deal with tab controls.

----

I think you need to become conversant with the ResponderChain, and with the idea that the NSTabView is just a subview of your window's content view, and that the controls you put there send their events up the responder chain for you. If all you specifically need to know is which subview you are in, study the documentation for the NSTabView class: there are several methods for getting and using this information. I suspect that you are at a very early beginner stage of using the Cocoa frameworks, and for your specific curiosity, you just need to become a little more familiar with those ideas. I think more than anything, you will be a little shocked by just how easy it is.

----

I'll add that you're putting the cart before the horse, so to speak. You seem to be jumping into creating an application before really going through the proper learning process. Start with Apple's introductory Cocoa and Objective-C documents. They'll teach you how to use Interface Builder (ie, dragging a tab view onto your window and wiring it up to an outlet so you can talk to it). Then read the documentation on NSTabView itself to get a feel for what methods it offers. This is basically a case of not reading the manual and (unfortunately) nobody's going to read it to you, so it's up to you to improve your research skills, which are a concrete *requirement* if you hope to do any useful development.

----

Incisive analysis there, and one of the major reasons why all the "Conceptual" sections of the documentation have been created, and why at least some of them are very worth reading.

