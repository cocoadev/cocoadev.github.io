---
layout: page
title: BindingsInARealApplication
---



see also, CocoaBindingsDiscussion for more discussions of bindings-related issues

There are, however, some interesting examples of Cocoa Bindings as follows, embedded in *working* applications:

http://homepage.mac.com/mmalc/CocoaExamples/controllers.html

----

Bindings IMHO seem to be more work than doing things without them if you really need to control user interaction and object creation.

Several comments:


*It almost goes without saying that not every nook and cranny of your app may be amenable to bindings...

***Actually, it was a simple case IMHO if you bothered to read my question and the replies.**

** re-factoring happens*; the post had been **idle** for two months prior to being re-factored - not that this *in itself* justifies the attempt to do so.

***There is a big difference between a re-factoring and a replacement.**

*' you yourself left it that way with a note that you were going to try "a few things"

***No, I ended it with a question.**

*you seem to be suggesting that Bindings is more trouble that it is worth (sometimes), and cite as an example an existing app that may *or 
may not* be entirely amenable to strict MVC structure. (Given your discussion of its minutiae, it is impossible to tell.)

***Actually, trying to prevent an invalid object from entering the data model does not go against MVC. My requirements were quite clear starting with my first post going forward.**

*From the top, you seem to be questioning the *fundamental* utility of bindings not only in your particular (real) application but as a technology in general.

***Not at all. They are cute and time-cutting when applied to where they are useful. I have just lowered my expectations from them, for now... (10/27/2004) BTW, If you think bindings are some stellar word-changing technology, you have obviously not worked on other platforms. This is just a common, notify me when something changes callback (yawn) using the runtime introspection capabilities of Objective-C/C++.**




----

Ok, I am about at my wits end with bindings.

I have a document app, that has many other sub-windows (and info) that are not documents. The information is loaded and saved durring application start and exit respectively.

My problem is this.

I am starting to adapt some of those panels to use bindings. I am starting to feel that either "I just don't get bindings" or "Bindings are NOT ready for prime time beyond a very simple app".

An example (note that I already have all of this handled without using bindings).

I have panels that control a "table-like" sets of data. These are outside of the documents I handle and cannot be treated as preferences.

I need to be notified of additions/changes/deletions, before they happen, when they happen, and after they happen, and I need to validate the entries at least on a "by object" basis before they are added to the model. I need to notify other parts of my application (after validation) that things have changed. A blank object should NOT be added to the model before it is considered valid, and NO, there are no reasonable defaults that would be considered valid.

I did add the index accessors to the receptive nib controller (now the mom of the model), and bind an NSArrayController to it.

Up front, everything was working fine. Now, what I need to know is how to get the bindings beast under control. So far, in my latest incarnation, I have written more code (or approching it) than without bindings. And Oh, I still can't figure out how my model is to be notified about changes. - replaceObjectIn<Key>AtIndex: is never called on my controller/model and even though the documents say it should, - get<Key>:range: is never called either.

Is this a case of Apple hyping a tech before it's ready?

----

I'm glad you're giving the technology a try - we can only improve things if developers try it and provide feedback...

If you're using the array controller to create and insert objects (add:(id)sender and insert:(id)sender actions) you can be "notified" of insertion before they happen by subclassing the array controller and overriding the newObject method (defined on NSObjectController); You can also use this method to properly create and validate your new objects before insertion - or return nil from newObject if object creation/validation failed...

I'm not sure where you implemented the insert, remove, replaceObject in Key methods, but you need to implement at least the insert and remove variants, the replaceObjectIn<Key>AtIndex method is a optional method for performance...  And it should be implemented on the object you bind the array controller's contentArray binding to...

Once the insertion is done - you can find out about changes to the array of the array controller by observing it. Something like 
   [arrayController addObserver:myObserver forKeyPath:@"arrangedObjects" options:nil context:NULL]

Hope that helps

Ron

----
Ron,

Thanks, The insert, remove, replaceObject in Key methods are all on my nibs controller, also keeper of my model contained in an array. In the case of a new object; If I derive my own NSArrayController and override newObject to handle the creation of a new element, how am I supposed to distinguish between the object being created by a human in front of the screen so I can drop a sheet from the panel for data entry (which a generic non-UI object shouldn't have a reference); from an object being created from say an automated process?

It seems that having to override NSArrayController to keep it from adding a invalid object to my model automatically, and also have a reference to it in the models controller just to be notified of changes, plus the derived NSArrayController having a reference to either my window, or back to the nibs controller, is cluttered and producing much more object "cross-talk" than without bindings in the first place, I am starting to believe that bindings is confusing the model more than simplifying it.

I did remove the UI add action off of the NSArrayController -add: and pointed it to a action on my nib controller to intercept the addition so I could drop a (bound) sheet I was also using as an inspector (reuse is good) to handle the input, but it would seem that the bound panel insists on having an object in the model before hand. Will I have to unbind and rebind the panel depending on the purpose, or should I have yet another NSObjectController in the nib pointed at the NSArrayController.selection, or a new object, to handle it?

----

When you override newObject, you can assume that it's triggered from user action... when that's not the case, use addObject: instead, passing in an already validated object... 

The controller layer will inevitably have connections to your model and view classes... historically in a pure MVC app, the controller layer was the bit that was least re-usable... Bindings tries to move beyond that so that the controller classes provided are more resuable... By the nature of software engineering, there will be cases where the reusability falls short of the goal... I don't think that subclassing the array controller to intercept more user interaction breaks the deal tho... Hopefully I can help... 

You probably want the add: action to trigger the sheet to run - have a model object in there.. when the sheet is done running, invoke addObject: on the array controller with a new object configured from info you pulled from the sheet...  If you don't want a partially initialised object added, then you wouldn't set up another object controller to point to the array controller's selection - that would mean the object's in the array controller before you run the sheet... 

I would create a dummy object (maybe just an NSMutableDictionary?) and use it as the model object for the sheet...  once the sheet finishes, you can continue by creating a real model object that you can insert, fill in all the values from the dummy model object, then message the array controller (probably self at this point) addObject:<realObject>

Hope my explanation isn't too obfuscated...  =)
Ron

 ----

Stand-By ;-) I am trying a few things.

One thing I nociced though is that I *aways* receive in my -observeValueForKeyPath:ofObject:change:context: a NSKeyValueChangeKindKey key of NSKeyValueChangeSetting with NSNull old/new and nothing else. It does not matter what action I am performing. I was hoping for the rest, Insertion, Removal, Replacement. What's up with that? I register with the following...
    
	[m_arrayController addObserver:self
		forKeyPath:@"arrangedObjects"
		options:NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld
		context:NULL
	];


----

Does Apple use bindings in any of its applications?

