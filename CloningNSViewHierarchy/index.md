---
layout: page
title: CloningNSViewHierarchy
---

Recently, I've been working on an application in which I would like to duplicate the idea that iTunes uses to create the criteria for making a smart playlist.  So far, my explorations have lead me to use the Interface Builder to create an NSView in which I've placed my controls, etc. to represent the item to be repeated (i.e. this is equivalent to the individual criteria for a smart playlist).  I've also created a class which is able to lay out its subviews in a nice list and behave properly when placed inside an NSScrollView.  So far, so good.  I have two controller classes, one to manage the list view, and one to manage an individual item view.  All this stuff lives in a single nib.

Here's the interesting part....  At runtime, when I'd like to add an item to the list, I want to clone the item controller and its view along with all the subviews, and I want all the connections between the objects to be preserved.  This is important because it allows the controller class to mantain all its links to the various controls from which it reads the user's input.  I accomplish this by having the controller class implement NSCoding (which subclasses of NSResponder also implement), and then simply use NSArchiver and NSUnarchiver to create an in-memory archive of the controller, view and subclasses, and then immediately unarchive everything thus (in theory) creating an exact copy - references and all - of the controller / view / subviews which I pulled out of the original nib.

As in the iTunes smart playlist builder, each item's view has an NSButton subview which creates a new item and hides the add button.  This is where things go wrong.  For a reason I cannot determine, when I press the add button I get a segmentation fault (signal 11, SIGSEGV) which indicates I've got a bad pointer somewhere.  Funny thing is, the control never makes it to the method designated as the action.  Unfortunately, I can't seem to figure out how this is happening.  I'm guessing that it has something to do with how I'm creating my clone of the original item view, but I can't seem to track it down.  I'm hoping that someone more clever than myself has some ideas...

AndrewMiner

How come you are going through the cloning process and not just creating a new view ? I don't know how you structured this but you could group the interface elements in their own subclass of NSVIew. Whenever you want to add an element create an instance of that view and add it to your overall view. This way you don't have to go through the cloning path you are just handling with freshly created objects. When dealing with "views" in this small scope I find it easier to just put model and controller into the view and deal with it as on Object.

HaRald

I didn't really follow why you have to clone your views. Put your view in a separate nib from the window, then just load the nib every time you want a new view.

MikeAsh

Panther has the NSNib class which will load the nib file (but not instantiate it), then you can peel off as many copies as you want without incurring any disk overhead.

