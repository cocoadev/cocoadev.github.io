---
layout: page
title: MVCDifferences
---



I've seen several different approaches to the MVC paradigm which conflict with Apple's. A search for "Model View Controller Paradigm" on Google brings up quite a few. The main difference I see is that the view and model communicate directly, where as in Apple's MVC, everything goes through the controller layer. Does anyone here have experience with these different approaches? If so, which do you prefer? -- RyanBates

----

A lot of people smash the MVC label on what they do -- most who do GUI work is probably closer to ModelViewPresenter.  But what really counts is to seperate model and view code. I.e. you do not make the NSTableView able to sort an array etc.

Personally I think MVC the way Apple previously did it sucks big time, because I have to write much more code than previosuly with absolutely no added benefit. I.e. when connecting a table view to an array all sorts of glue code had to be written to support drag'n'drop, column sorting, re-arranging a.s.o. and very little of this was re-usable.

The way they are headed with bindings improves things, althrough I do not think they are there yet, seeing as they insist on keeping the controller classes.

For example if I have a model which play an mp3, and that mp3 has a name, I should just be able to setup a connection (i.e. one line of code) binding the name of that mp3 to the title of the window (or whereever I wish to display it).

The way Apple wants me to do this is, if I understand correctly, to instantiate an ObjectController in IB, bind the title of the window to that controller (although the title is not an exposed binding property) and further bind the "object" of this object controller to somewhere in my model...

----

Apple may want that, but it seems to work out all right if you bind views to model objects directly, without an NSObjectController in the way. But Scott Anguish (iirc) has warned against this, and says that text fields might not work properly.

----

Finally someone else mentions this!  Whenever I point it out people tell me I am insane and that they are the same!  Nice to know that I am not alone.

As for which works best, Apple's sounds good on paper but I don't actually find it to be as useful as the other, more common, interpretations.  Primarily, this is because I find that Apple's interpretation leads to objects which exist mostly to be conduits to move data between other objects.  Also, Apple's interpretation doesn't make as strong statements about visibility that the other models do.  Bidirectional visibility is something I don't really like because it is hard to tell when you are starting to deviate from the design.  For example, I really like another interpretation which says that the model should never know about the controller.  I find that this is very powerful in that you always know that a model isn't starting to become a controller since it can't *control* anything.  As soon as you find that you want the model to be able to talk to the controller, this interpretation lets you conclude that the model actually is becoming controller-like and you will soon have to refactor.  In Apple's interpretation, such refactoring hints aren't as apparent.

There are other things I find better about the modularity of the other interpretations and the way in which they can be connected, very concretely, to higher-level concepts.

--JeffDisher

----

Yes, there is a few problems with binding directly from model objects to view objects (and vice versa). One being that the views are sort of special in that they expose (non-observable) "virtual properties" and overload the     bind:toObject:withKeyPath:options: method, another being that the target of a binding must have both a set *and* get method, e.g. you cannot bind to     titleWithRepresentedFile, even though the system would only need to send the message     setTitleWithRepresentedFile: (when the observed property change), but currently it also sends the get message (to see if the value has changed).

Those familiar with the Amiga might remember BOOPSI and MUI, the latter was the de facto GUI library and the former was the run-time OO system of AmigaOS, not much unlike ObjectiveC -- in MUI all attributes could be observed and trigger various actions, one action could be to set that attribute on another object -- observation could also be sort of "filtered", i.e. perform only the action if the value of the attribute change to 3 -- basically the action was a method to call, but that method could have arguments like the "trigger value" (i.e. the value of the attribute), and since setting an attribute was just a method invocation, bindings could also be made like this -- as of such, the two systems sounds very similar in description, but they do differ slightly, mostly in philosophy I think, which affect the flexibility of the systems dramatically and one system allowed for a lot of "end user innovation", while the other comes with a lot of pre-made modules, but the abilities for the end-user to mix and match seems too limited, but time will tell if I am just being pessimistic... :)

*�That's one hell of a run-on sentence. :-)*

----

This is an issue that I am very interested in, since controlling (no pun intended) complexity is often a bottleneck for large projects.  Here are a couple links that may be interesting to readers.

"Model-View-Controller framework" http://www.object-arts.com/EducationCentre/Overviews/MVC.htm
"Model-View-Presenter Framework" http://www.object-arts.com/EducationCentre/Overviews/ModelViewPresenter.htm
"Design Patterns in User Interface Design" http://www.cs.vu.nl/~pgroene/Vakken/HCI/hci.html

- Eric H

----

I think you can smooth-up the "don't access the model with the view and vice versa" rule a little bit with the usage of notification centers. I normally throw messages from the model to the controller or view class. There I can decide with component will react on that message. And anyway most of the view classes are in an nib file, so if I want to do more complex things than just showing the data, I have to use the controller class. I think the most important thing is that the model isn't changing the view and vice versa. That gives you the possibility to rip away your view and do something else with your model + controller (for example integrate it into a network application or so...).

--ThomasSempf

----

I'll throw in my voice to second a lot of what I've heard here - The model knows about itself and other 'collaborators' in the model. It also knows about what you consider to be 'core' objects, such as NSString or possibly NSNotificationCenter. If it responds to notifications, they should be of a 'business' nature ( whatever 'business' means to your app ). It knows nothing about the controller or the view.

The view knows about the same 'core' objects as the model, but not about the model specifically.

The controller layer knows about the model and the view. It can map data ( boring yes, but usually worth the abstraction if reuse of the model is a goal ), respond to business notifications to update the view, and respond to view events to trigger business functionality in the model.

The nature of Objective-C can kind of play with the definition of 'knows about', though. If you can map a button's action to a message on the model, does the view really 'know' about the model? It knows about selectors and objects, but thats core to the language, not really anything specific to your model. I am assuming that the model method doesn't know anything about the view when it responds to the message. I don't know that I'd care for a design that does this kind of thing heavily, but I don't think that it specifically violates the letter or spirit of MVC to do such a thing.

The nature of your app can also play with traditional views of MVC as well. I can imagine a reasonable OO design behind Interface Builder - the purpose of IB is to manipulate Buttons, panels, panes, etc. One could argue that to IB, the traditional view object IS the model. Of course, the current state of the model is ALSO represented by buttons, panels, etc. So one could imagine instances of NSButton that represent the model , controller objects somewhere, and other instances of NSButton that are being used to display the current state of the model instance.

I'm clueless about how IB is designed. I'm not even saying that's the best way to do it. I'm just trying to demonstrate that MyClass can legitimately fill more than one role in MVC depending on the 'business' of the application being written.

--TimHart

----

Interesting read regarding this subject at:

http://www.c2.com/cgi/wiki?WhatsaControllerAnyway

