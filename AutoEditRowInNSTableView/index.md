---
layout: page
title: AutoEditRowInNSTableView
---

I'm a Cocoa newbie and I'd like to know how to create an NSTableView which automatically sets the editing focus ring around a cell when a new entry is created. Similar to the Bookmarks panel in Safari or the "source" table in iTunes.

ie. both of these views have a '+' button below the table, when pressed the newly created row is already focused for editing.

Could someone please explain how this can be achieved (preferably using Cocoa Bindings)

----

Being a newbie, it's necessary to tell you this for the first time: RTFM. ;-) (*which stands for "Read The, um, Fantastic Manual"*)

Just connect the + button to your NSArrayController's add: or insert: outlet. This is all automagically handled. The following link should be VERY useful to you:

http://www.cocoadevcentral.com/articles/000080.php

Wow, could you cut back on the arrogance? The "solution" you offer has nothing to do with the actual problem being offered. Don't jump on every opportunity to insult a (self-proclaimed) newbie. --FC

----

You've misunderstood me...connecting the Button to the NSArrayController's outlet will enable the addition of rows to the table, but you need to double-click the new cell to actually edit the contents. If you have a look at the two example apps cited above, you'll see that when the '+' button is pressed you are automatically in edit mode in the new cell, this is what I want to know how to do.

----

Perhaps the "easiest" solution (which doesn't use Bindings, unfortunately) is to write a small subclass of NSArrayController that overrides addObject: to add your functionality. Something like this might work...
    
- (void)addObject:(id)object
{
     int count = self arrangedObjects] count];
     [super addObject:object];
     [tableView editColumn:0 row:count withEvent:nil select:YES]; // Edit first column
}

In this case     tableView is an [[IBOutlet (declared in your class's variables as     IBOutlet NSTableView *tableView;) which should be hooked up to your table view. The     col and     select parameters can be changed if you want. Once you have finished your class, just read the header file into Interface Builder, change your NSArrayController instance to your subclass, and connect the tableView outlet. After that...it should work! NOTE: this is untested, as I am in the middle of a couple of other projects.

--JediKnil

----

Thanks - this worked.

