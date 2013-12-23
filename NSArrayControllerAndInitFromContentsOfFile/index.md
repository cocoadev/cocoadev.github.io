---
layout: page
title: NSArrayControllerAndInitFromContentsOfFile
---

 

Hi All,
So I have an internal NSArray that I want synced to an NSTableView.  I created an outlet to an NSArrayController and insert/remove from the array controller to keep everything synced with my NSArray and the NSTableview.  I can save files just fine (the NSArray just holds NSMutableDictionaries), but I am stuck in figuring out how to open them and read them into my NSArray and update the table.  Right now I have something like:

     ...
[myArrayController addObject: anObject];
//The above inserts an object into myArray and updates the tableview
...

//this doesn't seem to work (well it does if I 'Open' twice...wierd), I am doing this from memory so there may be a typo or two
[myArrayController removeObjects: myArray]; //clear the current table, works fine
[myArray release];
myArray = [NSArray initWithContentsOfFile: @"somefile.xml"]
[myArrayController setContent: myArray];
 

Any ideas on how I can manipulate myArray and let myArrayController know when I am done to let the bound NSTableView to update?

----

Sure. After you setContent, do the following:
    [myArrayController rearrangeObjects];

However, if you're directly manipulating the contents, you need to use proper KVO notifications. See the Apple documentation (Key-Value Observing) for more details - this really isn't difficult. You tell the array controller 'I'm going to change something', change something, then 'I'm done changing something'.

----

Ah ok, finally get it.  The "batch import array" section was useful here:

http://homepage.mac.com/mmalc/CocoaExamples/controllers.html

