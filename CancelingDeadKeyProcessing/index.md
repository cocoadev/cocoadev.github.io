---
layout: page
title: CancelingDeadKeyProcessing
---

Is there some way to cancel the effect that a dead-key (like option-e on a US keyboard) has on the input server?

If I have this code in a custom non-text control

    
-(void)keyDown:(NSEvent *)keyEvent
{
     // under some circumstances..
     [self interpretEvents:[NSArray arrayWithObject:keyEvent]];
}


and I type "option-e, e" on my US keyboard, then the second keyEvent received above has characters @"�".  That's usually what I want, but sometimes I'd like to be able to cancel the hanging dead key so that this sequence

"option-e", <cancel-dead-key>, "e" 

results in my getting a keyEvent with characters @"e".  Can I do that?  Or is there some other way, like by using the field editor, that I can make things work?  I do want my control to be the first responder whenever anything gets drawn.

-KenFerry

(This is for KFTypeSelectTableView, a page I'll create shortly.)

----

How about     [textView unmarkText]?

*
- (void)unmarkText

Removes any marking from pending input text, and disposes of the marked text as it wishes. The text view should accept the marked text as if it had been inserted normally.
*

    [[NSInputManager currentInputManager] wantsToDelayTextChangeNotifications] returning YES might mean something interesting as well.

----

*(Sorry, edit collision! I was adding this while you were writing.)*

I should add this:  I thought it would work to let the field editor interpret key events, then send it     unmarkText when I wanted to get rid of any dead keys hanging overhead.  However, it didn't work; the fieldEditor didn't mark any text when it interpreted the dead-key event, and sending it     unmarkText had no effect.  The code looked like this:

    
-(void)keyDown:(NSEvent *)keyEvent
{
     // under some circumstances..
    NSText *fieldEditor = self window] fieldEditor:YES forObject:self]; 
    [fieldEditor interpretEvents:[[[NSArray arrayWithObject:keyEvent]];
}


I thought maybe the field editor needed to be the firstResponder for the input manager to find it (to mark the text), but temporarily making it first responder didn't work either.

-KenFerry 

----

Have you tried sending     unmarkText to the     currentInputManager? NSInputManager conforms to NSTextInput as well. Don't know if it'd work, but it's worth a try.

----

I'll try, and I'm also trying to figure out what wantsToDelayTextChangeNotifications means.  One problem is that     [NSInputManager currentInputManager] seems to return nil when I call it from anything other than     keyDown:, and this cancel isn't going to be triggered by a keyDown message.

-KenFerry

----

The manager doesn't have any marked text either.  Setting aside the original question (which seems like it oughta be solvable by itself), I think it would work if I could get the manager to mark text in the field editor (which I think must partially delegate control of dead-keys to the text object).  What do I need to set up to make that happen?

----

I managed to work around this problem for my code, but I never did figure out how to cancel a dead-key.  You can see my workaround in the KFTypeSelectTableView source.

-KenFerry

