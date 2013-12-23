---
layout: page
title: MissingKeyValueChangesOnButtonPress
---




I am a newbie to Cocoa/Interface builder, so please excuse my ignorance if this is a simple question.

I am contributing to a Cocoa application (a subversion plugin for Finder), and I have hit a snag.  I have created a new model class that has a couple of KVC fields and then supports two actions.

In IB, I have created an NSObjectController, an instance of my Model, and then bound the UI elements to use the controller to get the KVC values.  This all works :-).  However, I have done something wrong.  When I click on the button, my model gets the message, but any changes in the field that had focus before this click are not sent to the model. Obviously, I have done something wrong in wiring up the UI, but I cannot figure this one out.

Apparently, the design I have created records the changes in the fields in the UI elements (I can see them), and sets them in the model on change in focus, but somehow my button click does not create a change in focus, or the controller is not in the loop of that sequence of events.

Any and all help on this would be greatly appreciated.

Charlie

----
If I understand your setup properly, then this is something I have come across before. It seems that pushing a button isn't enough to commit edits from an NSTextField. To get around this, in the bindings window in IB, check 'Continuously Updates Value' so that the NSObjectController sees all changes as soon as you make them.

Cheers, Julian

 ----

Julian is correct that a button won't cause your text field's editing to end and checking "continuously updates value" in the field's bindings options will help. To expand the idea, however, a *button* is not needed here at all if you're using bindings with the text field. You're sort of missing the entire point of Bindings by requiring a button click to sync the field's contents with your model. I'd recommend going back over the Cocoa Bindings Programming Topics documentation and carefully reading the explanation of what it is and how it works. If you take your time and absorb it, you'll see what I'm saying.

