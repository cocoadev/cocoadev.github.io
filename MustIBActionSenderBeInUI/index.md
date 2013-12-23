---
layout: page
title: MustIBActionSenderBeInUI
---

Is the target-action formalism strictly for visible user interface objects?

Are there programmatic means of accomplishing the same thing?

I think what is in the back of my mind with this question is something
like threads or distributed objects, about which I know nothing.

Sorry if this is a bit too academic (or too dumb - or both!) a question.

Are there any circumstances in which an IBAction method is NOT in fact connected to an
object (the sender) in the visible user interface? In other words, can the sender object
be somewhere besides the interface?

Does (id)sender always have to be some object that appears as an instance in IB?

Obviously, there are other ways of programmatically specifying a method to execute using selectors.
----
The sender can be any object. Actions are just regular methods with a particular signature.

In the code below, the targetOfAction object will receive the 'doSomething' message with 'anyObject' as the sender.
    
[targetOfAction doSomething:anyObject];

----
It is often useful to trigger an IBAction method that updates the UI when some external event triggers a change in the model. I frequently do something like:
    
if (dataAltered) [self inputsChanged:self];

when in fact the UI inputs have not changed, but they need updating because the object whose data is being displayed has changed for other reasons.

It's only confusing to use IBAction if there are no UIEs calling the method.

-Ralph Henderson

----

I think you are on to what prompted my original question, Ralph.
I know that the IBAction tag is only for the benefit of IB to parse such things in new classes.

I have since explored the documentation a little, and so far the only class I found with setTarget: was NSControl.

I was just wondering if other classes  besides the canonical UI controls have a method like that. I will be looking.
(And realizing that there are other design schemes - delegates, maybe, for example? - to do much the same thing)

Maybe this just leads in the direction of designing home-brewed controls.

----

setTarget: only sets what object will receive the control's action message.  For something other than a control, what would this mean?

The caller to an IBAction method does not have to have any notion of a target.  Like Ralph said, it's a simple method call.  There are (in general) absolutely no requirements on the calling object.  This is, of course, quite unlike one would expect from C++ or Java, and is just one example of the pure beauty of Objective C!

It sounds to me like you might be wanting to use something like notifications.  Check the documentation for NSNotificationCenter.

----

Thank you profoundly for devoting some thought to this, and responding.
I actually got on the trail of notifications since last time - your comment gives me confidence I am on the right track.

----

Just to add my two cents. The target-action paradigm is clearly designed to be used by controls and other UI objects. If you attempt to use it in other cercumstances, you will most likely run into some limitations. For example: if the target is not specified, the action will be sent down the responder chain - which only includes View and View-Controller related objects. I find Apple's documentation on communicating with objects to be very good. You can read more about the Target-Action paradigm here:

http://developer.apple.com/documentation/Cocoa/Conceptual/CommunicateWithObjects/Articles/TargetAction.html

-- RyanBates

