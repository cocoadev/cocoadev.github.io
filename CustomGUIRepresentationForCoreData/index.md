---
layout: page
title: CustomGUIRepresentationForCoreData
---

Anyone know how to represent the entities in Core data with custom GUI?

For example, if i add an employee, i want a graphic of an employee to be shown in a Custom view area in the document.

----

Uh... subclass NSView?

----

Slightly more clearly:  This question is sufficiently general that the answer is "use cocoa programming".  There is nothing special about CoreData - a custom GUI is a custom GUI.  :-)  Read up on views and drawing at http://developer.apple.com/documentation/Cocoa/Conceptual/DrawViews/index.html .

----

Try FlowChartView, that's exactly what the demo file does.

----
Sounds like the real question here might be: "How do I connect my view so that it reflects the state of the data store?" The easy way is to use bindings. Add an appropriate controller, such as NSArrayController, to your document's nib file. Bind that controller's managedObjectContext parameter to your document's managedObjectContext attribute. Then, in the controller's Attributes inspector, make sure that the controller is in "entity" mode and set the entity that it controls to whatever entity you want to display in your view. Using your example, you'd set it to your model's employee entity. Next, create an NSView subclass that knows how to draw employees and has a binding for the array of employees that it's supposed to draw. Bind the view to the array controller's arrangedObjects attribute. Run your app, add some employees, and smile at all the code you didn't have to write to make the right thing happen.

Two things that are generally true about both Core Data and Cocoa Bindings are: 1) they can save you an awful lot of work; and 2) you can't make them work for you until you understand what's going on. If you don't understand parts of the preceding paragraph, you probably need to sit down and work through some documentation and also look at some of the Core Data sample code that comes with Xcode. -CS

