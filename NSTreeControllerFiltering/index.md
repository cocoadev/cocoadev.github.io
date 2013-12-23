---
layout: page
title: NSTreeControllerFiltering
---

see also OutlineViewBindings

How do I implement filtering in NSTreeController? This is relatively easy to do in NSArrayController, as shown here:

http://homepage.mac.com/mmalc/CocoaExamples/controllers.html

The Filtering Controller example shows how to filter an NSArrayController by subclassing and override -arrangeObjects:. However, NSTreeController has no such corresponding method. I need to display only objects with a certain flag set in my outline view - how do I do this using NSTreeController?

----

I have a single entity, "Item."  Item has attributes for name and children.  Name is a string, children is a to-many relationship for other items.  Obviously this would be well-handled by an NSTreeController, but I need to do it in an NSArrayController so I can filter the data and display the results in a table.

I have two controllers, an NSArrayController of root items (with the predicate parent == nil), and an NSArrayController bound to the children content set of the root controller.  How can I make the other array controller bind to ALL of the items in the root controller, not just one level deep?

----

You don't need to do it in an NSArrayController to filter data. You can apply a filter to an NSTreeController with only minor adaptation of Mmalc's Bindings examples:

http://homepage.mac.com/mmalc/CocoaExamples/controllers.html 

 ... look for "Filtering Controller" example and adapt it.

Also...shouldn't you be using an NSOutlineView for tree/recursive data?

----

I have an NSArrayController bound to my root objects (or folders).  I then have an NSTreeController bound to all of the children of the selected folder.

So basically, on the left I have a table view with a list of folders.  On the right I have a list of items in that folder (in an outline view, because it is recursive).  I'm in the process of adding filtering to the selected folder.  I have it working fine for filtering ALL ITEMS, but not just the items in the selected folder. To filter all items, I do the following:

Swap the outline with a table.  That table is bound to another array controller, which is bound to the item entity.  Then I filter that.  My question is, how can I bind that search controller to only the items in the selected folder, instead of all of the items?  Thanks

----

I'm not entirely certain, but I'm looking at a similar thing (nested to-do list items bound via an NSTreeController) and it looks like I may have to subclass NSTreeController to get this to work. -- RobRix

----

I'm not sure if this will help any, but I've written an NSTreeController subclass that seems to be able to filter the tree based on a predicate.  Check it out at http://end.com/~speth/FilteringOutlineEdit.zip - it's got my class dropped into the OutlineEdit sample code.  --Jim

----
Gosh! This hack really looks evil! :-)

----

Straight up... it's pure evil from the fifth dimension. :)  It's a fun brain-bender when I try and explain it.  Should I try?  --Jim

