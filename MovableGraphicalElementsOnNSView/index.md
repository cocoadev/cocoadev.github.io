---
layout: page
title: MovableGraphicalElementsOnNSView
---

I'm trying to make a program that will have an NSView instance and 10-100 little graphical elements (that will probably look like filled squares at this point) that I will need to create, drag around that NSView and delete. 

At this point, I have a custom NSView class that facilitates all that, but the mouse hit checks and movement is made rather ackwardly, imho. That is, on mouseDown, NSView goes through an array of elements, and checks which one got the hit, then it relocates it on every mouseDragged.

So, heres the question. I think I can do it with a bit more elegance, i.e. subclass one of the Cocoa classes that can respond to mouseDown, dragged. mouseUp events and do all the movement and modifications to themselves. Now, what's the least complex class that can do it? I looked at NSView, but it looks too heavyweight. NSCell has some primitive mouse detection actions, but I'm not really sure how they work. 

So, which class can I subclass, or is there any better way of doing things?

Thanks,

-DimitriT.

----
See Apple's Sketch example.  It is on your hard disk already.
Why do you think searching an array for the element that was hit by a mouseDown event and then repositioning that element on mouseDragged events is awkward ?  Consider the framework's standard use of subviews and how the framework uses NSView's -hitTest: method.  How do you think -hitTest: is implemented ?  I guess my point is that even if you make elements responsible for moving themselves, the container of the elements still needs to select which element was hit by the mouse and tell that element to start moving itself particularly when elements overlap.  I don't think you will find a less "awkward" technique than the one you have already tried...but if you show us code, we may be able to improve a specific implementation of the technique.

----

You could use NSView but you are correct in that there is a lot of overhead associated with an object that 'does it all for you'. 100 elements on-screen may be pushing it, though, depending entirely on your goal. For efficiency, it's usually best to use NSCell to its fullest if they're simple, homogenous little controls with few pre-determined states. This is exactly why NSTableView uses cells -- tables typically have many rows/columns of 'elements'. For more complicated tasks, NSCell may not have everything you need to implement it easily, though, so you may be in for a bit of work either way you go. The best way to approach NSCell is to read all about it. Everything you can. Twice. ;-) It's a bit of a leap of logic for beginners but it's well worth it.

- JNozzi


See also (for a view / subview approach): DraggingNSViews

