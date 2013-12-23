---
layout: page
title: FirstResponder
---



The FirstResponder is the first object in the ResponderChain that is given the opportunity to respond to AnEvent.

see MenusAndDocuments for how to use the ResponderChain in document-based applications

----

In the context of InterfaceBuilder, the FirstResponder instance icon is a proxy for the first object in the responder chain that implements an ActionMethod.

InterfaceBuilder implements this by setting the ActionMethod's target to nil. See NilTargetedAction.

----

It is faster to send a message directly to the relevant object than to use the responder chain. So, if possible, connect things up to each other directly in the NIB, rather than going through the first responder.

Or so I've heard...

----

Yes, it is faster but it is also less flexible. You really want new, undo, redo, cut, copy, paste, delete, select all and such things to work on the first responder, not a hard coded target.

It may also allow you to do a more modular program -- remember that both your key window and main window delegate is also part of the responder chain, plus the applications delegate. So you can do "fall back" implementation, e.g. you may normally have the key window catch a message, but if this window is missing (perhaps not loaded yet?) you can let the application delegate catch the message (and perhaps load the appropriate nib file and forward the message).

Also remember that performance is not an issue here -- finding first responder needs to be done each time the user selects an action, so this can happen I'd say maximum 10 times pr. second. And finding the first responder takes far less than 1/10th of a second, so there really is enough time :-)


----

I can add actions to the FirstResponder in the InterfaceBuilder class browser, but  I can't add outlets.  Why is that??

----

No, it doesn't make sense to add outlets, because FirstResponder isn't a real object, it's just a proxy object for the "first responder" - the first real object that will respond to a NilTargetedAction at runtime.

----

The First Responder class in Interface Builder have some action methods which I do not see defined in the documentation. Anybody know what their purpose might be?

*play: (not in any appkit or foundation header, probably related to the SoundKit where you could record and playback sounds)
*record: (same)
*resume: (same)
*run:
*start: ~ NSMovieView
*stop: ~ NSMovieView, NSApplication

I am asking because I do have some stuff where they could be used, e.g. record/play for recording and replaying a macro and run to run an external script.  But I guess it would be better to introduce my own action methods named recordMacro, playMacro, runScript etc.

----

Those method names are just placeholders.  if 'record' or 'run' makes sense in your program, go for it.

