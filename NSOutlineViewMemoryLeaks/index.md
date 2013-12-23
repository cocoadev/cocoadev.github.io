---
layout: page
title: NSOutlineViewMemoryLeaks
---



I want to populate my NSOutlineView with items from the file system.  I need to keep the FileSystemItem objects around (or do I?) because they are represented in the outlineview with a check box.  So if someone checks/unchecks the checkbox I make a change to the object in my own data structure and reload the outlineview with the changed objects.
That takes up a lot of memory!!! Am I making some kind of rooky mistake?
For Instance: I'm using my documents folder as the only root item in the NSOutlineView. My documents folder contains 29789 items (830 MB). I create an array that holds that many FileSystemItems (I got the FileSystemItem class from the Developer Examples).  I do that in the awakeFromNib and as soon as the window of my app shows my app already uses 54MB of RAM.  Clearly too much.  And at that point I haven't even loaded the data into the Outlineview.  Any ideas?  Thanks for all the help

greetings mOmO

----
Have you tried ObjectAlloc?

----
Well I don't think that it is a memory leak, per se.  That is I know where the memory is going.  It's going into the array that holds 29789 items.  I guess I'm wondering if I'm using a totally inappropriate programming technique to handle all these items.  but maybe keeping track of 29789+ items in an outlineview is just going to cost a bunch of RAM.  I don't know.  Thanks though.  I tinkered with objectAlloc a bit.  I'll definitely use it in the future to debug memory issues.

thanks mOmO

----
ObjectAlloc can not only help you find leaks, but it can just tell you how many of what kinds of objects exist in your app so you can see what's taking up so much space. But now I see that with that many items, it's only about 2kB per item, which isn't really that excessive.

One thing you could do would be to cut down as much as possible on the data you keep around. I believe NSOutlineView forces you to keep at least one object per node in memory, but you could make this object be very minimal, such as just the filename. Then when the outline view asks you for object values, you can read them off the disk right then, rather than reading everything into memory beforehand. This sounds slow but since you can only fit so much on the screen at a time, I think it won't be noticeable and it should help a lot with your memory usage.

----
Thank you, that is certainly worth a try.  I'll give that a go and see.  
Any other ideas?

----
Is there any way you could use just one object, and have it determine which node the NSOutlineView is asking about dynamically? (Can't remember the API.) Or maybe use the delegate's     -outlineView:willDisplayCell:forTableColumn:item: to fill out minimal placeholder objects on demand? Then you could control caching manually.

----
I don't think so. The problem is that there's a couple of data source methods like:

    - (int)outlineView:(NSOutlineView *)outlineView numberOfChildrenOfItem:(id)item;

Where you're expected to return information about an item and are given nothing but that item. And NSOutlineView doesn't retain the items, nor is there any mechanism for finding out when it no longer has a reference to them, meaning that you can create them on demand but you can never destroy them. If I'm wrong about this and somebody has a way to destroy them early, I'd love to know, but as far as I'm aware this is not possible.

