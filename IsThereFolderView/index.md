---
layout: page
title: IsThereFolderView
---

I couldn't find any documentation on FolderView. Is it safe to assume there is no official documentation about it? Can anyone offer any suggestions on how it should be implemented? 
Also, i was wondering if it was necessary go gather all the data before I try to lay out something using NSOutlineView. Anyone have any experience in this area?
----

FolderView? What is it? If you want to show a filesystem hierarchy in a NSOutlineView look at /Developer/Examples/AppKit/OutlineView/

----

folderview is almost like hierarchy tree except you only can see one level at a time (just like when u open your drive and do view as icon), works well when you have many nodes.
i've read the sample, only question with that is do i need to find out all the children nodes?

----

there is no "folder view" for cocoa, but it should be "fairly" straightforward to implement.
 there's probably SEVERAL approaches to this problem, but I'll outline one of them:

subclass NSControl.  we'll call it  NSFolderView
subclass actionCell, call this Guy NSFolderCell

you have to override the control cell junk in your  folderView class, so that you can allow for multiple cells.
override the mouse  methods so that you can check your click for contact with a cell, and what to do about it
override the cell creation methods and Subclass NSObject to create a Data Source object  (read and read and reread the table View class spec)
create a conduit between the Control and the datasource.

tell your Cell how to draw itself.
 

ok. that about covers the overview, its a good exercise in OOP and a great way to get into how tables, Controls, and Cells work.
I suggest reading first about Controls and Cells, then move on to Browsers (it's older, and teaches a few interesting things about hierarchies)
after that look at Table Views and Outline Views.
none of them have the free form placement of the Cells (the icons with their titles) that you get in the finder, but that's fairly easy to figure out.
check out drag n drop, which is very well supported in NSView (which NSControl is derived from, and therefore NSControl supports drag n drop the same way NSView does).

as for your question about NSOutlineView... I'm not sure what you are asking... if you want to display information that you can't get to, then you are not going to have much luck.  But if you are asking whether or not you need to Own the information, then No you don't need to own the info.  Outline views all rely on Datasources, which are generally translators.  the outline view tells the datasource "hey, I need this Information!" and the data source's job is to get the information and then provide it in a way that is easily digestable for the View (usually a NSString).  they set it up this way so that the Outline View is flexible enough to be useful in almost any circumstances, and you yourself can decide what Kind of information to feed it.


may I ask what kind of application you expect to use a "folderView" for?  are you making a finder replacement? something to browse the filesystem with? or are you emulating the workflow of the Finder, leveraging its ease of use for some "other" kind of data?

----

ok, my datasources are actually coming from a database over a socket connection so in order to avoid a bunch of select queries to build the tree.  i want to be able to do a select whenever the end user clicks on the parents only.  is that possible?

the reason for a "folderview" is actually just an alternative for users (or trying to do what our web version is capable of).

----

if I understand your question, yes. it is possible.
the controls that use Matrices, (tables browsers, outlineViews etc...) all ask for data as they need it, they never ask for data they aren't just about to display.

you should make a local datasource that acts as an intermediary between your database, and your matrix class (whatever you choose to work with).
the intermediary should be able to look up information in the database, based on its index (hierarchical indexes such as: item 1 of item3 of item4... etc...) or its value (item named "alpha" of item named "beta" ...)

that way, your local datasource is the only object that needs to know how to get to the database, and it only gives values (probably as NSNumbers or NSStrings) away.

----

You may want to check out UKDistributedView, I've never tried it, but I think it does what you are looking for: http://www.zathras.de/programming/cocoa_stuff.php

