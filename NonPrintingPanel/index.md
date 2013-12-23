---
layout: page
title: NonPrintingPanel
---


My application has a main window with a custom view and a couple of panels that I use to control that view.
The custom view accepts first responder status and the main window also makes use of an NSColorPanel.

The problem I'm having is that when I want to print the contents of the view I don't want to have to make sure that it is the selected window.  I don't have to worry about it with the NSColorPanel - why is that?  My panels are subclasses of NSPanel.  What do I have to implement in MyPanel class to get the same behavior?


----

I'm not sure, but you could try

    -(void)print:(id)sender
{
    [[self nextResponder] print:sender];
}

----
Nope.  Thanks, though.

----

Does calling     [self setBecomesKeyOnlyIfNeeded:true] help? Just a wild guess, but that's often how I debug :)

----

i may be misunderstanding the issue, but if you always want to print the custom view no matter where you are in your app, just hook the Print menu item in IB directly to your custom view instead of first responder.  And if you want to send it on from there, you can override the print method in your custom view and send it down the responder chain from there.

