---
layout: page
title: PhilosophicalCoreDataProblem
---



Hi folks,

I have come up against a bit of a philosophical dilemma in my work with CoreData. It has to do with ModelViewController and where drawing code belongs.

I have built my app around CoreData, I was a Tiger Early Starter, and it's a database like app so it was a natural fit. I've done about 6 months work on it and it all works nicely. I am cutting my teeth on Cocoa with this app, and so it has been rewritten twice from scratch as I found my feet. I am now confident that I have a solid understanding of my problem domain, and how to solve it using Cocoa/CoreData.

So now to the dilemma. I have some objects which need to display a representation to the user on screen. They are Pages, the be specific. I have borrowed a lot of ideas from mmalcs Bindings totorials, which it seems are the canonical examples of bindings, and are comprehensive enough to cover almost anything. In particular the Graphics Bindings example, which is a dot view type example. Anyway I'll assume you're familiar with it - if you're not then you won't be able to help me!

In mmalcs Graphics Bindings example, and in my reinterpretation of it within CoreData, obects which will be drawn (conform to the Graphic formal protocol) specify their drawing code in the model object (a la MVC). I have stuck with this design, although I always wondered about it.

Surely a model should not, in strict MVC, be drawing anything? Shouldn't it be instructing the View to draw something thus maintaining reusability?

As it is, I have implemented the bindings and drawing code (drawRect:) into a category of my model object, which is a subclass of NSManagedObject. I have dubbed this arrangement CLManagedGraphic - and it works really well. It is a CoreData and bindings aware, subclassable graphics engine. In fact I love it.

*Correction - the above paragraph should read: ...and drawing code (drawInView:).... -Alex *

Except for my nagging moral doubt - is it the right thing to do? Please help...

-AlexClarke

----

*The view itself is responsible for drawing in the MVC paradigm. The view should be aware of your models and draw whichever ones you tell it to. Consider a view that displays multiple images all on one canvas. The image is stored as an entity (model) with properties like 'data' and 'frame' (size and position) and maybe even 'shouldBeDrawn'. The view itself must know how to find all these image entities, determine if they '-shouldBeDrawn' and draw them according to their properties. The view should also listen for core data notifications (like NSManagedObjectContextObjectsDidChangeNotification) and redraw all or part of itself as appropriate.*

----

Thanks for your response. this confirms what I had suspected. 

That leads me to the next question, why did mmalc use this design in his example? I guess I'll have to ask him personally for an accurate answer to that, but does anyone have any ideas? Convenience?

For those not familiar, basically the Graphic Bindings example, AIUI, boils down to the following structure:

Model <Graphic> - Controller - Custom View

The nice thing about the design is that the bindings are keyed to "bounds properties" and "non-bounds properties" of the Model object. When any of the bounds properties are changed, the view is redrawn. This lends itself very nicely to Core Data implementations.

The problem with this approach is that the drawInView: method in the Model object would seem to violate MVC, and therefore lead to brittle code. 

I've also realised that I misnamed this page... it should be called "WhereDoesDrawingCodeBelong?" or similar. Is it possible to change CocoaDev page titles? If so how?

----

There's nothing in-and-of-itself wrong with having a model object which represents a drawable thing to implement rendering code, particularly in a graphical application where the drawn representation of the model object is an intrinsic property of the object, rather than a loosely associated property. Consider:

1. a graphic program with model objects including Square, Circle, Diamond - like Sketch - the graphic representation of these model objects is intrinsic, and so it's reasonable as a first approximation to associate drawing code with the objects themselves...

2. a software design program with model objects including, Class, Relationship, and Attribute - like Rational Rose - the (UML) graphic representation of these model objects is not intrinsic, Booch diagrams will look different and so it's a reasonable tactic to break out drawing from the model objects.

Our old diagramming app for MOSXS used approach 1 for drawing since graphics were just graphics, and the approach kept the code simple and minimized class count. It worked out very well.

Circumstances where you might break out drawing code include cases where there is no intrinsic graphical representation of a model object. 

----
I think the real problem is that, in reality, there are two kinds of model objects - The data model objects AND the view model objects. I have yet to figure out the best way to connect the two.

Example:  We could have an entity called 'Note' which would have properties like timestamp, pitch and velocity. In our application we'd like to have multiple views to the same data simultaneously, so we have a to-many relationship from a Note entity to the Note representation.
 
This is why we cannot use drawInView: added into the Note class by a category, at least not without some indirection. Still we'd like use objects to represent Notes in our custom view, perhaps using an NSActionCell subclass, because the representation might require so much code that it clearly belongs to a separate class. It would be so non-OOP to assume the actual content view to do nothing more than send [note drawInView:self] messages.  And Ta-daa, we now have what I've seen to be called 'view model' objects. View model objects should NOT be stored in a persistent store, because they are 1) generated (I meant: allocated on the fly, as the visibleRect changes etc.), 2) temporary and 3) have nothing to do with the actual data model.

So my question is: What is the best way to deal with this?  Ideally, the model would remain completely unaware of the representational view model(s) and yet, the custom view should be able to bind directly to the array controller managing actual data objects.

-MM

----
I like to keep the NSManagedObject subclasses that are generated "pure".  I never edit the generated code.
I often provide categories on the NSManagedObject subclasses to provide application logic in the Model.
In the View, I sometimes add categories to the Model objects (whether Core Data or manually managed) to aid with view concerns such as drawing.

For example, if I have a Core Data NSManageObject subclass called "RoofTruss", I may have a RoofTruss+Quartz category to draw the roof truss using Quartz.  I might have another category called RoofTruss+OpenGL that draws the roof truss using OpenGL.  In both cases, the drawing category is maintained in the View layer and not the Model layer.

I was inspired by the way NSAttributedString is in Foundation Kit (Model) but drawing and display attributes are defined in a category in the AppKit framework (View) .

----

I'm actually facing a similar dilemma. Although I'm still in the design stages of my project (and by that I mean it's still in my head). My imaginary project involves a lot of graphs each with their own underlying data. I imagined an interface where the graphs themselves could be positioned anywhere within a parent view (or just as there own little windows). I'm hoping that this information (the location of each graph) could be persistent and managed by CD. I'm kind of new to Cocoa in general, but I have just been blown away by the ease and flexibility of CoreData and bindings for developing interfaces. Is this a bad design decision? Is this possible?

----
If graph position and whether a graph is grouped with others or alone in a window are per-user or per-application preferences, then that information should be stored in preferences.  If graph position etc. is intrisic to the document (e.g. the whole point of the document is layout information and all users should see graphs the same way), then store the information in the core data persistent store.  It is easy enough to store Boolean and float information with Core Data.  Float information is handy for specifying positions and sizes...

I have been know to store an archived version of the document's NSPrintInfo as data in a managed object.  That way, document size, margins, etc. is stored with the rest of the data in the persistent store.  That also makes document size chnage and margin change automatically undoable.

----
An approach I've done recently kind of lends itself to a lot of objects in the nib, but it's been useful in the sort of MVC compartmentalization. The core data's purely storage, the view is a table, and the data store/delegate links not only to the core data controllers, but a single-per-nib graphics engine and a single-per-nib cache. The graphics engine holds the graphics settings (Icon size, gutters, font attributes), but the core data holds the color settings and icons. When the core data changes, the data store forces the cache to update. When the view polls the data store, it sends the core data and cache to the graphics engine.

Would that be a Model-View-Controller-Cache-Engine?

As long as it works, I suppose.

