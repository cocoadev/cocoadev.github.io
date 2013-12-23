---
layout: page
title: NilTargetedAction
---



An nil targeted action is an ActionMethod attached to a control but whose target is unknown until runtime. Nil targeted actions are created by adding actions to the FirstResponder proxy in InterfaceBuilder and connecting them to controls. Most often menu items are connected to nil targets.

The benefit of nil targeted actions is: rather than being directly connected to a particular object (class instance) such that only that object can respond the messages, a nil target action works through the ResponderChain starting with the object which has the 'key' focus. In practical terms, each object in your UI could implement the same ActionMethod suited to its own needs. The Objective-C runtime would then invoke the method belonging to the current first responder ie, the one with the key focus.

----

This is the thing that makes the Cut, Copy and Paste menu items work correctly regardless of the object that has the focus. -- l0ne aka EmanueleVulcano

To respond to NilTargetedAction, an NSView has to be able to become first responder, which is done in NSView subclasses by implementing the method     -(BOOL)acceptsFirstResponder and have it return **YES**.

----

To send a NilTargetedAction from code use NSApplication's      - (BOOL)sendAction:(SEL)anAction to:(id)aTarget from:(id)sender  (or UIApplication's      - (BOOL)sendAction:(SEL)action to:(id)target from:(id)sender forEvent:(UIEvent *)event on iOS) with a nil target.

