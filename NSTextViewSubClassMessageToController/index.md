---
layout: page
title: NSTextViewSubClassMessageToController
---

I have a need to capture the RETURN/ENTER key in a NSTextView control.  I have subclassed the NSTextView and overridden the keyDown: method.  
    
-(void)keyDown:(NSEvent *)theEvent 
{
    // test to see if the user hit the enter key. If they do then process the text edit.
    if( theEvent characters] characterAtIndex:0] == 13 )
    {
        // let the controller know about the return key was hit.
        [[[[NSNotificationCenter defaultCenter] postNotificationName:@"ReturnKeyHit" object:self];
    }
    else
    {
        [super keyDown:theEvent];
    }
}


I am using a Notification to message back to the controller (or any other listening object).  Is this correct way of handling this.  I can't figure out how to use the Target/Action pattern to do the same thing.

Any suggestions on using a custom control to message to controller object?  Should I be using the delegate pattern? 

John

