---
layout: page
title: CommunicationBetweenObjects
---



Simple question.

I have a controller object that loads it's own nib, has outlets and actions, etc.
I have another object that instatiates these objects.  How do I communicate between the two?  That is, when an action is triggered (a button push in it's associated window) how can the object that created that object get wind of the action.

The controller object has accessor methods, so I can easily reference it's instance variables but I just don't know how to reference any actions that occur.

I think I'm missing something conceptually.

----

Ideally, IMHO, when you instantiate a controller object, it should not have to communicate with objects other than it's view and model objects. In real life(TM) you can use a couple of options: pass a pointer when creating the controller object (    id cont = [MyController controllerWithOwner:self];), or you can use notifications (my personal favorite).

I assume you mean something like this extremely common case:
    
@implementation MyMainController
- (IBAction) someAction:(id)sender
{
    id data = [self getData:sender];
    id cont = [MySubController subControllerWithData:data];
    [subControllers addObject:cont];
    [cont showWindow:self];
}
@end

- JeremyJurksztowicz

----

I agree that it would be much cleaner to have the object not have to communicate with the object that created it, and be completely "self sufficient," as it were.
My problem, though, is that I want the created object to be able to use the same AsyncSocket that already has an established connection in the host object.

