---
layout: page
title: AccessingCoreDataFromCustomObject
---

I have a question about how to access the data in core data from a custom object.

I have been successful at doing the following:
    (1) creating a core data entity using the graphical interface in xcode.  I am calling the entity "Points" and each point has the attributes "name" (a string), "x" (an int 16), and "y" (an int 16).
    (2) creating a table and a plus/minus button to add and modify this core data using interface builder.
    (3) adding an array and bindings to the table and plus/minus buttons so that the data is correctly stored and shown.

I have created a custom view that I can successfully draw in as well.  What I would like to know is what code can I use in the drawRect method in my custom view to access the data stored in the core data part of my program.  I would like to be able to get all of the points in the table and use their x and y values to plot them in the custom view.  How do I get this data?

----

Have a look at the docs for NSArrayController. You can access all your objects from it.

----

I have looked into it, but it looks like there are two possibilities for getting the information from core data through the NSArrayController:

(1) Adding custom bindings to my custom view.  The NSTableView allows you to bind the values of the columns to an NSArrayController through the "value" bindings.  A custom view only has "hidden" and "toolTip" as possible bindings.  I wish I could get IB to show a Points binding for my custom view that would allow me to get the array of points from my PointController (which is an NSArrayController).  I think this is possible, but I have not been successfull at figuring out how to do it.

(2) Programmatically getting the data from the NSArrayController from within my custom view.  If only there was a way to say something like "NSArray *array = [PointController getArrayOfPoints];".  Then I could use an enumerator to go through and plot all of the points with their respective x and y values.  I could even draw the name under each point.

Anyone have any idea of how to do either of the top two things?

----

Another option, and probably the best for your situation, is to have your controller-- be it an NSDocument subclass, NSWindowController subclass, or some other sort of controller class-- bind the arrangedObjects of the NSArrayController to some 'values' array on your view, and then have your view get the objects it needs to work with from there.

----

*Anyone have any idea of how to do either of the top two things?*

Yes: 

http://www.devworld.apple.com/documentation/Cocoa/Conceptual/CoreData/index.html

and

http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaBindings/index.html

