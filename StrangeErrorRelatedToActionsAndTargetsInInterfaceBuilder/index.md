---
layout: page
title: StrangeErrorRelatedToActionsAndTargetsInInterfaceBuilder
---

I'm having a strange error when I run my application:

2005-03-14 20:13:37.855 blah[3102] Could not connect the action logIn: to target of class General/NSApplication

This only happens when I connect my General/NSButton which is in an General/NSView to an action in a General/NSWindowController.

This code works fine.

It doesn't show an error if I set the target and action in code. However I'd rather do this in IB, does anyone have an idea what is causing this problem?

----

Make sure the class in your nib = class in your project. Drag and drop to update your nib.

*Shouldn't that be "=="? :)*

----

Also, remember if you're just putting together a basic app with the default General/MainMenu.nib, you'll need to instantiate a separate controller class in IB to be the target of your action, since File's Owner in this default nib will be an General/NSApplication class, even if you try setting it differently. Note If you're anticipating your app will grow to more than one window however, you'll get more flexibility and performance by splitting every window into a separate nib by itself.
