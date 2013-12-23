---
layout: page
title: EscapeKeyEvent
---

The easiest way to capture the EscapeKeyEvent from a text object (e.g. NSTextField or NSTextView) is to set the delegate to an object that responds to     control:textView:doCommandBySelector:. Here's an example:

    
- (BOOL)control:(NSControl *)control textView:(NSTextView *)textView 
    doCommandBySelector:(SEL)command 
{
    if (command == @selector(cancelOperation:)) {
        NSLog(@"escape key has been pressed");
    }
    return NO;
}



--zootbobbalu


----

Does anyone know how to capture an escape key event in a cocoa app? The escape key doesn't send a flagsChanged or keyDown message to the firstResponder. I make sure that the field editor is not associated with any views when I try to capture an escape key event. 

I tried every combination of the following key words when searching the net:

cocoa 
escape 
key 
bindings 
NSEvent

but I don't get anything

I know you can change the defaultKeyBindings to get the desired effect, but this is at the cost of changing the key bindings for apps launched after the fact.

----
My first responder gets escape keyDown messages, and plenty of them.

    
- (void)keyDown: (NSEvent *) event {

   if ([event keyCode] == 53){
        NSLog(@"Escape has been pressed");
     }
  
}


One thing that always bugs me is apple has constants for every damn key imaginable except the ones I want to use, so I am stuck using key codes.

----

Don't you love Cocoa!!! I wasn't sure if the object in question was in fact the firstResponder since you informed me that I should be able to receive these events, so all I did is ask the key window what object was the first responder and found out that the window delegate doen't stand in as the first responder even when the window is the first responder. I guess you shouldn't assume anything about delegates, even if they appear to be handling most of the desired user interactions of the object they're delegating for. 

So now the question is: 

If the NSWindow class forwards keyDown events to its delegate, then how can I be certain that nothing will ever stand between the window and the delegate to the window when keyDown events are involved? 

Where should you implement your version of the keyDown method, in a subclass of an NSWindow or a window delegate?


----

I think that perhaps passing it along to the delegate is best. Something about the ModelViewController, I do not think anything can really stand between.

----

The escape key gets converted to the **_cancelKey:** action, or **cancelOperation:** in Panther.

