---
layout: page
title: ObjCParadigmQuestion
---



Hi, I'm new to ObjC and I was wondering how to do something.

The situation:

I'm creating an application that (should be) fairly straight forward. It's an application that allows the user to drag and drop files onto a custom view and then files are processed and only the JPGs are added into an NSTableView. All of my components work on their own, but it fails when I try to make it all work together.

Nib Design:

The Nib is very simple. There's one window that has a NSTableView dropped on it, and then a custom view (custom class: FileDragDrop) that sits behind it, allowing users to drag and drop files "into" the table view. The NSTableView is not editable by users--only through dragging and dropping in this manner.

File setup:

* AppController: a sub-class from NSObject and is instantiated in MainMenu.nib; this takes care of the actions to process the files (in this case adding a meta tag to them).
* FileDragDrop: the class for the custom view that lays behind the NSTableView that receives the drag and drop action
* DataSource: the dataSource for the NSTableView
* PhotoFile: a class that creates an object to represent each JPEG file; when files are dragged onto the custom view, the JPEG file paths are used to create this object, and then the resulting object is stored in an array


The problem:

Now, like I said earlier, all of these components work individually. Testing with NSLog(), I can see that all the data exists within the class. My problem is how do I link all the data together? DataSource and FileDragDrop both need access to the NSMutableArray that contains all of the PhotoFile objects. The FileDragDrop needs to be able to tell DataSource that new files have been dropped and that it's time to update the view. How do I make it so that all of these objects can talk to each other?

----
You can put all those items inside your Nib files. Create outlets in your controller for them. Make sure all of your objets also have outlets for the controller. Then, any object (such as the drag-n-drop view) can see the controller. The controller can then relay the data to another object (such as the data source). In addition, it can tell the display to update itself once the new data is there.
----

