---
layout: page
title: NSTextFieldEndEditing
---



OK, I'm not sure the title is a good description, but here goes: I've got several NSTextFields in a window for a user to fill out, and once done, they press a button that send out the information over the net. The textfields get disabled when the information gets sent. The problem is that if the user clicks the button right after entering text a field, the information won't "stick"; its value is either blank or whatever was in the field before it got edited.

I could try to get the value before disabling the field, but that doesn't prevent it from disappearing altogether when the field is disabled. Is there a way to fix this?

----

Try     [window makeFirstResponder:window] before doing all of your disabling.

----
try calling     validateEditing on your fields before disabling them; setting up a delegate or notifications might also work. -Seb

*    validateEditing works great!**

----

This situation arises also when closing the window. For example, in Mac OS X applications, preference setting windows are usually immediately effective - you don't have to hit a "save changes" or "apply" *shudder* button. This works fine by implementing preference setting code in each preference element's "didEndEditing" action and specifying the delegate. This does not work if a user makes a change to a text field, and then closes the window. To make the last change stick, add this to your NSWindowController's windowWillClose: method:

    self window] makeFirstResponder:[self window;

----
Using continuously-updated bindings will also fix the problem. This will also mean that your settings will take effect if the user makes a change and then switches windows without pressing any keys or closing anything. The non-bindings equivalent would be to listen for controlTextDidChange: for all of your fields, but that's a big pain.

----
I am using this:

if ([[mWindow firstResponder]respondsToSelector:@selector(validateEditing)])
    [[mWindow firstResponder]validateEditing];
- SJI

Of course, this is not working for me in a new app I am working on.  No idea why....[mWindow makeFirstResponder:mWindow]; does though.

