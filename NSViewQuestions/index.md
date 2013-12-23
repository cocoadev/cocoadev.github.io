---
layout: page
title: NSViewQuestions
---

I have an NSTextView in a window and would like to get information about that window.  How do I refer to the window that contains my view?

----

Provided that you have an outlet to your text view, named myTextView, you can get a pointer to its window like this:

    
NSWindow * theWindowOfMyTextView = [myTextView window];


This can be found in the documentation for NSView (which NSTextView is a subclass of).

see also HowToReferToTheWindow 

----
Question: How do you prevent sub-views from drawing?

----
You don't. -ChrisMeyer
-setHidden:YES, but please don't abuse this feature.

----

I have a NSView residing in my NSDocument window, and I have three instances of NSView in the instances pane of my document. How would I have an action that sets one of the instance pane NSView to find a new home in the NSView on the NSWindow?

----

    [window setContentView:aView]

Note that this may or may not be good UI!

So, whatever object the action method is on is going to want to have outlets linked to your three instance views, of course.

Another way you could do this would be with an NSTabView, with or without the actual tabs.

----

I was using a NSTabView but it was too cluttered, and was hard to get to other tabs without messing up the NSTextView

----

*You could just hide the tabs in the tab view - I believe this is the recommended way of doing this now*

----
What does that have to do with it,     [window setContentView:aView] I thought I wanted a NSView in the window, and three instance pane NSView

----

*sorry, that makes absolutely no sense.*

----

**You have three views that you want to switch in and out of the window, yes? Then make them outlets on whatever controller object you're using, we'll call them     objectOne,     objectTwo, and     objectThree. When you want to put     objectTwo into the window, you call     [window setContentView:objectTwo]. That'll put that view into the window.**

----

*I don't have any views at all - I'm not the one with the question; I thought you were referring to my post about hiding tabs.     [window setContentView:] would (obviously) set the window's entire content view, not a particular subview of the window.*

----

**Yes, but, he's got three views in the *instances pane,* i.e. views not in a window.**

----

*That he wants to insert in a NSView residing in a document window. I take that to mean said NSView is not the window's contentView.*

----

Wow, I think this page is creating more questions then answers. From what I can tell, two questions are being asked:

**Q: How do I create a tabless NSTabView?**

----

First create an NSTabView in InterfaceBuilder - best to make the necessary tabs before making it tabless. Make it tabless by clicking "Tabless" in the "NSTabView Info" window. To switch the current tab through InterfaceBuilder, double click the NSTabView and change the "Display Tab Item:" field in the info window. To change the current tab programmatically, use the     [tabView selectTabViewItemWithIdentifier:anIdentifier] method (or one of the other "select..." methods). NOTE: you do not need to create external NSView instances for a tabless NSTabView.

----

**Q: How do I change the main view of a window?**

----

First, you must create another view instance that you want to change to. Probably the best way to do this is to use InterfaceBuilder and drag a Custom View to the "Instance" pane. You can then apply that view programmatically to the window by calling     [window setContentView:aView]. This will resize the new view to fit the window. NOTE: The window retains its content view, but after setting another content view, it releases the old content view. This means, if you want to use that old view again your object will need to retain it before changing the content view.

----

How do you hide a view?

----

*10.3 introduced a     setHidden:(BOOL)flag method to hide views, although the code below would presumably still work.*

Here's how I hide a view:
    
[theView retain];
[theView removeFromSuperview];
theSuperview = [theView superview]; <- is this OK, what's the superview here?
[theSuperview display];

To make it visible again, use something like this:

    
[theSuperview addSubview:theView];
[theSuperview setNeedsDisplayInRect:[theView frame]];
[theView release];


- DigitalCow
----
Another technique to hide views is to place them in a borderless NSTabView with a blank tab as the other tab and then just choose which tab you want to enable. Although this is a waste of system resources.

Still another technique involves as NSView category (see http://www.omnigroup.com/mailman/archive/macosx-dev/2001-February/009600.html ).

-ChrisMeyer

----
Question: When should you subclass NSView and when should you not? 

----

Answer: You should always subclass NSView. A direct instance of NSView isn't good for anything besides drawing blank rectangles in your window.

For example in Apple's example app, Sketch, they chose to make the canvas area a NSView, but they didn't go as far as making each drawing item a NSView - even though doing so would have saved them from writing a whole lot of mouse intersection code. 

----

I guess there is some overhead with subclassing NSView. So where do you draw the line? And what exactly does NSView do behind the scenes that makes it more inefficient than writing your own mouse intersection code?

-Wolfy


----

wolfy, 
look closely at the documentation of the relationship between  NSCell, and NSControl (NSMatrix is good too).  And theres alot of documentation about interface/implementation... you should read it. Essentially, a View is the rough equivalent of a Canvas, setting up the coordinate system and the framing for the drawing elements, the Cells.  You are right in assuming that there is a lot of overhead in drawing views, and thats part of the reason for the separation of views and cells.  Another reason is that the functionality of Views and Cells, is fundamentally different, and if we were to combine the two, the resulting object would be less useful in terms of reusability and subclassing.

I haven't done any profiling of NSViews, so i don't know what its doing behind the scenes that might make it inefficient for mouse intersection code, but I do know that since NSCells assume the graphics state and coordinate system of their parent views, they Absolutely draw faster than NSViews, and That would make cells more effective during mouse interaction.  

because Cells help you separate interface (the view) from implementation (cells) you can do neat tricks like break up the functionality of one visual device (for example: a graph with: rulers, bezier paths, and points) into many cells, making the code easier to follow, more specific to the application (a ruler certainly acts differently than a bezier path), and make elements (like rulers) reusable. If you used views to do this, you would create work for yourself because you would have to manually keep the coordinate systems lined up, and you would be slowing down the redraw.

good luck, and dont abuse your views
----

http://www.stepwise.com/Articles/Technical/NSCell.html
Introduction excerpted:
"One of the first design elements that experienced programmers question when learning the Application Kit is the relationship between the NSCell class and the NSView class.

NSView is the abstract super class that encapsulates drawing and event responding in the Application Kit. NSCell is an abstract super class for drawing and responding to events within a View. Why have both classes? Don't they overlap?


NSCell is a "helper" class

NSCell and its subclasses are used as "helpers" within NSView instances.
An NSView is a powerful generalized class for handling all possible graphical and event interactions with users. In order to support all of that generality, NSView instances must maintain a large amount of graphical state. Each NSView instance has its own coordinate system, a reference to a parent view that contains it, a collection of sub-views (or child) views that it contains, and well defined transformations between parent/child views. NSView instances can have associated Postscript graphical states (gstate). NSView instances have page layout and printing capabilities. The NSView class has a powerful and sophisticated set of methods that are always called in a certain order (usually automatically) in order to setup a graphical context, execute custom drawing commands, and restore the previous context.

In Contrast, a NSCell is very simple and light weight. NSCell instances typically maintain a small amount of graphical state relevant to the particular NSCell class. NSCell<nowiki/>s usually do not have their own coordinate system or even direct knowledge of the coordinate system in which they will draw. NSCell<nowiki/>s do not normally have a parent/child relationship with other NSCell<nowiki/>s or NSView<nowiki/>s."

----
General question: should you subclass a NSView to make a NSToolbarItem for its     setView: method? I used a standard NSView in my NibFile that isn't subclassed and it works, but I don't know how much of a good idea it is. - PietroGagliardi

----
It really doesn't matter.

----
Pietro: This is another case where you **must** read HowToAskQuestions. You got a vague answer because you've asked a vague question. Maybe you're being vague because you're afraid people will figure out what you're doing and steal your idea if you disclose too much? In any case, the answer to your question as you asked it is, "It depends entirely on what you're trying to achieve." So ... if you want a more specific answer, you're going to have to state very clearly what you're trying to accomplish.

----
QUESTION:
Is there a best practice way to create a status bar in the bottom of the view. I would like to implement mine as they have done in Safari with the ability to show/hide the status bar.
Also ... how do I make the UI look similar to e.g. the new leopard finder. The textured version of NSView is darker than the new Finder.

----
Look into BWToolkit (http://www.brandonwalkin.com/bwtoolkit/) for the status bar at the bottom of a window. -- Alexsander Akers (http://j.mp/9cyKGm)

