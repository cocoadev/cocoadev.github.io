---
layout: page
title: QuittingNonDocumentAppUsingDelegate
---

see also DelegateObject

see also DelegationAndNotification

see also NSApplication

When and where do I implement

    - (BOOL)applicationShouldTerminateAfterLastWindowClosed:(NSApplication *)theApplication

My aim is to quit the application when the user clicks the window's close button.

----

A DelegateObject receives messages from another object and can act on behalf of the other object. It's a way to add behavior to an object without having to subclass. Lots of common Cocoa objects have a delegate outlet that you can connect in InterfaceBuilder to a DelegateObject that implements the behavior you want on behalf of those objects.

Methods implemented on the delegate, like the one to which you refer above, generally contain "Will" or "Should" in their selector name.

The method you are interested in must be implemented by the delegate of the NSApplication instance. You can set it in two ways:


* Programmatically by using     [NSApp setDelegate:yourDelegateObjectHere];
* In Interface Builder (if you're using NIB files for your application) by instantiating yourDelegateObject and connecting it to the delegate outlet of the NSApp instance (the File's Owner of your MainMenu.nib file).


----

**How can I program the Close button to quit the application?**

----

If your application is a one-window app, put     -(BOOL)applicationShouldTerminateAfterLastWindowClosed:(NSApplication *)theApp{return YES;} in your app delegate. If your app is not a one-window app, **don't do this**.

Note first that this is considered by some (most?) to be contrary to interface guidelines for one-window apps, and your users *may* find this behavior both unexpected and annoying.

----

Unfortunately, I have two windows in my app.  Is there any other way of doing this?

----

Keep track of how many windows are open. Or bite the bullet and don't quit -- it's not the Mac OS X way, so it bears repeating that you risk confusing your users. If you still insist on doing so, create a controller object and make it the delegate of your window (if you only want one window of two to be able to terminate the app, make it the delegate of THAT window.) Implement the windowShouldClose or windowWillClose method and send NSApp a     terminate: message from there. But really, you shouldn't. Really.

There is more discussion of this subject under NSApplication

see also CreatingObjectThatWillUseADelegate if you want to create an object that *sends* delegate messages (as opposed to receiving them).

