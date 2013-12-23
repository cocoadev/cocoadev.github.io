---
layout: page
title: SuppressWindowMovementOnTexturedWindows
---

I have a textured window, and in this window I have a custom view object on which the user can draw using the mouse.  If I set the window to use the normal window model (i.e. non-textured) then everything works fine.  However, if I change the window to textured, then when the user goes to draw on the custom view object, the window moves along with the mouse.

I would still like the user to be able to move the window around by grabbing outside of my view object.  How can I suppress mouse clicks and drags inside my custom view object from going to the window it is sitting on?

----
*Even when just trying this with IB's Test Interface this works as expected. Is your custom view not set up as first responder?*

----
I have made a connection between the view and the window and designated the view as the window's initial fist responder, but the problem persists.  

I found a page in the apple docs : 

http://developer.apple.com/documentation/Cocoa/Conceptual/General/BasicEventHandling/Concepts/General/FirstResponder.html 

It describes that a custom view should override the boolean functions  <code>accesptsFirstResponder </code> and  <code>resignFirstResponder </code>.  I added these functions to my custom view, returning YES for the former and NO for the latter, but they had no effect.  

I tried dragging a connection from the First Responder object in the instances pane of the nib window to the window object in the same pane, and to the view, but the object does not seem to allow connections in this manner.

----

This is pretty easy. Just add the following override in your custom view:

    
- (BOOL) mouseDownCanMoveWindow
{
    return NO;
}



General/EricForget

----

Aah!  That did the trick!  Thanks a million. ^_^
