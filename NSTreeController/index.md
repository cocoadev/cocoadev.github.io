---
layout: page
title: NSTreeController
---

The NSTreeController is a bindings compatible controller that manages a tree of objects, and is designed to work with NSBrowser or NSOutlineView controls. It provides selection and sort management. The root object can be a single object, or an array of objects.

NSTreeController is available in Mac OS X v10.4 and later

Apple Reference: http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTreeController_Class/Reference/Reference.html

----

NSTreeController may currently have problems when you provide Core Data content through bindings.  Using 'Automatically prepares content' may be a workaround when possible.  Providing content to NSTreeController through the contentObject, contentArray, or contentSet bindings has been shown to expose a bug that will cause your app to crash randomly at non-deterministic times.  Discussion in NSTreeControllerBugOrDeveloperError and NSTreeControllerBug

----

Some hints for implementing drag-and-drop in an NSOutlineView backed by an NSTreeController:
http://theocacao.com/document.page/130

----

With Core Data, I have an application where I manage two entities called "Entry" and "Group". A group can have subgroups and it can contain entries. The issue I'm trying to wrap my head around is how to represent this with an NSTreeController and NSOutlineView. I'm also wondering if I have my model set up correctly ("Entry" has to-one relationship to -group, "Group" has to-many relationship to -entries, a to-many relationship to itself via -groups, and a to-one to itself via -parentGroup). The examples and discussions I've found so far seem to be for just one entity that can contain itself (like "Mailbox"). http://www.southwesternsummer.com : Southwestern Company Internships for them who love programming. Since an Entry can't contain anything but Group can contain documents and other groups, I have no idea how to represent this. Any help would be appreciated. 

----

I was having trouble with addChild with a small project where whenever I would add a child to an existing item in my tree the NSOutlineView would display the item at the proper node level, in addition to a root level item representing the new item. The trick to resolving this was to set the Predicate (select the treecontroller and hit apple+1) to the value:

    
parent == nil


This appears to insert a nil root item into the tree, giving the outlineview a point at which it knows reverse recursion doesn't have to happen. I didn't see this noted anywhere in searching for it.

----

I too had to discover the "parent == nil" predicate idiom on my own when I first started using NSOutlineView with an NSTreeController on Core Data.  I strongly believe that this necessary idiom should be illustrated and discussed in the official documentation and/or articles, tutorials and books.  If you're going to use NSOutlineView / NSTreeController / Core Data together then you will at some point need that predicate and it doesn't make sense to force everybody to figure it out on their own.

----

FYI: I was receiving the following message in the following situation (with Core Data, NSTreeController, and NSOutlineView):

    
[95664:a0f] [<NSXMLDocumentMapNode 0x20002d160> valueForUndefinedKey:]: this class is not key value coding-compliant for the key parent.


-Entity Folder with optional, transient relationships parent <->> children with destination Folder

-Entity SmartFolder that had a Parent (inherited from) Folder

-NSTreeController of enitity Folder with "parent == nil" predicate

-Added a Folder and restarted application


Fix: Change NSTreeController predicate to "isLeaf == 0", where isLeaf is a boolean attribute.

P.S. This only works if Folders don't have children.

----

Just thought I'd add that while I agree it seems some predicate is absolutely necessary, those looking to solve this duplicate problem will have to devise a personalized solution, as the "parent" key is not universal, just these two posters' examples.  

For example, I use the key "isRoot", which is an attribute of my managed object, a BOOL, and by default set to NO. Then, when creating a new ManagedObject that you DO want to show up in your hierarchy, send it this message:
    
[aManagedObject setValue:[NSNumber numberWithBool:YES] forKey:@"isRoot"];

noting that everything is assumed to be NO until set otherwise.  As the previous posters stated, in IB set the treeController's predicate to "isRoot == YES", and now only you have the power to add items to this outlineView, via a KVO-compliant call such as:

    
NSMutableSet * hierarchyItemsChildren = [hierarchyItem mutableSetValueForKey:@"children"];
	[hierarchyItemsChildren addObject:newHierarchCompliantManagedObject];


Which brings me to my own questions:

1) Why is there no means of using the NSTreeControllers inbuilt methods for adding an item and then modifying the attributes of that item?  Using addchild: for example makes a new blank item, which then there is no way to set up as I want.  It also doesn't allow for adding items of different NSEntityDescriptions to the controller, since the controller automatically creates a new item of its set class.  I want a setItem:asChildOfItem: method!

2) More importantly, does anyone know how to specify whether a to-many relationship is a Set or an Array?  Just look at the docs; sets are practically useless and a pain to get things into and out of, while arrays obviously have a rich background API because we use them constantly.  But when I use mutableArrayValueForKey: my app raises an exception saying my vanilla managedObject doesn't implement that method for that to-many key.  Of course, mutableSetValueForKey works and therefore must be some kind of default.  I want to change that behavior... any ideas?

----

I described the process of avoiding duplicates in NSOutlineView bound to NSTreeController which is bound to something else here:
http://dchest.posterous.com/core-data-nstreecontroller-dup

----

Adding Drag support to an NSTreeController with Core Data

Part 1:
http://allusions.sourceforge.net/articles/treeDragPart1.php

Part 2:
http://allusions.sourceforge.net/articles/treeDragPart2.php

----

Does anyone have an example of supporting NSTreeController and drag-and-drop in an Outline view, without Core Data? Instead, using an old-fashioned custom model? Is there an NSTreeController subclass (like "DNDArrayController") that will do this?

There's a tutorial for just that here (bottom of page) - http://www.literatureandlatte.com/freestuff/index.html
-- Danny

----

Wil Shipley's published a couple of categories adding useful methods to NSTreeController:

"[...] a category of 'NSTreeController' that adds a -setSelectedObjects: method which takes your REAL objects - not the phony shadow ones - from anywhere in your content tree, and also adds an -indexPathToObject:: method which will tell you where one of your REAL objects is in the NSTreeController given the controller's current sorting and children-getting-method and all that, so you don't have to get your hands all sticky with NSIndexPath gunk.

"[...] an NSOutlineView category that adds one crucial method: -realItemForOpaqueItem:, so when you're given an item from bizarro-world in an NSOutlineView delegate or datasource callback, you can just turn it into one of your objects, and speak to it in your language."

Here 'tis:
http://wilshipley.com/blog/2006/04/pimp-my-code-part-10-whining-about.html

I couldn't get Will's code to work with my core data powered NSTreeController. However, the NSOutlineView extensions I found in DragAndDropOutlineEdit worked like a charm:
http://homepage.mac.com/matthol2/cocoa/page2/page2.html

----

Does anyone know how to get NSTreeController to immediately sort new objects whenever a new node gets expanded? I currently have a Core Data-backed class running through an array controller, which is hooked up to a tree controller. My sort descriptor is bound to my tree controller and it works for the initial display of content, but as soon as I expand any of the nodes the children of the expanded nodes are not sorted according to my descriptor. To get those to sort I have to click the column header again to resort. Has anybody else run into this problem or know of a solution? -- KentSutherland

----
Did you try calling -rearrangeObjects in your method to add objects?

----

I found something that maybe of interest. But since I don't have 10.5 yet, I can't see for myself. I found the link to this on a really obscure website. (Probably because it is still recent) http://developer.apple.com/samplecode/AbstractTree/index.html

----

... which of course is easily found searching the developer site.

----
I just stumbled across this very helpful example:
http://developer.apple.com/samplecode/SourceView/index.html

----
I've finished a blog post on using NSTreeController with Core Data in 10.5, the sample project has these principal features:

*Arbitrary sorting using an index - allows for a tree to be reordered via drag and drop and the state to be saved.
*Drag and drop
*Expanded item state saving
*Multiple entities in the tree
*Categories on NSTreeController, NSIndexPath, and NSTreeNode that make data access easier

Here's the URL: http://espresso-served-here.com/2008/05/13/nstreecontroller-and-core-data-sorted/
EDIT (from someone looking for solutions): the espresso-served-here URL is defunct, but http://jonathandann.wordpress.com/2008/05/13/nstreecontroller-and-core-data-sorted/ appears to be the same article.

