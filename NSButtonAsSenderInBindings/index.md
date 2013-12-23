---
layout: page
title: NSButtonAsSenderInBindings
---

An Interface Builder question:
I have look all-over but I can't find anything that tells me how to do something I would assume to be very simple: I want to bind an NSButton to an action using bindings, and have the button be the "sender" parameter.

Let's say that the method I want to use looks like this:     - (IBAction)toggleState:(id)sender.  If I specify the target in the binding as toggleSender: it gets called, but sender is nil.  What do I need to put as the Argument model key path so that the button gets sent?  I want to query the sender (NSButton) for its tag.  The same method may be used by a menu, that's why I would like to sort this out.  It's easy to do it as a connection, but I really want to know how to do it as a binding.

----
Why are you using bindings for this? Why not directly connect the button to the target object?

----
The toggleSender method is in a class that's the File's Owner of a MyDocument nib.  The button that I want to connect to it, is in a panel that's shared between all documents.  I can't seem to connect the button from one nib file (in the panel) to a method in another nib file (MyDocument).  And if I could, in a multiple document environment, with a shared panel, that connection I suspect will be static and won't be what I want (but I don't think you can make that connection anyway).  

Currently I need to put a relay (or forwarding) method in the panel nib file, File's Owner.  To make bindings work with multiple documents, the panel nib file already has an accessor called inspecting (which points at the current MyDocument File's Owner).  If I could use bindings, I could set the target model key path to inspecting and the Selector Name to toggleSender: and I wouldn't need the relay method (by relay, I simple mean a method that basically says     -(IBAction)toggleSender:(id)sender {[inspecting toggleSender:sender];}.  It's not a really big deal and the relay method works, it would just be more tidy through bindings.  All of the shared panel inspectors can use the same NSWindowsController sub-class, except now for the one that has the button.  It needs this relay method and thus another sub-class.

----
Maybe you should check out MakingNibsTalkToEachOther, it is very possible to connect an interface item to a method in another Nib by using FirstResponder.

----

Any time you try to "communicate between nibs" ask yourself if that's really the best way to structure your application.  In particular, what I often see is that attempts to "communicate between nibs" are really attempts to subvert the ModelViewController architecture that Cocoa is structured around.  Often what you *should* have is a self-contained nib that manages a particular set of tasks and whose File's Owner is some sort of controller object; not an NSObjectController, but an NSWindowController or a similar controller of your own creation.  (For example, if you're building an applicaiton where each tab in a tab control has a separate nib, the File's Owner of each would probably be an instance of a "tab pane controller" class you create yourself.)

Obviously the menu bar is an application-level special case, and a toolbar is a window-level special case.  This is what the ResponderChain was created for, and why you can target the FirstResponder with an action in InterfaceBuilder.  As long as your controller objects are instances of subclasses of NSResponder and manage their membership in the ResponderChain properly, your menu and toolbar items should behave as you need them to.

