---
layout: page
title: DuplicateArrayItems
---

Probably a really simple question, but I can't get a good answer. How do you duplicate items in an array? I have an NSArrayController, and I set up an action to do as follows:

    

- (void)duplicate:(id)sender {

	[arrayController addObjects:[arrayController selectedObjects]];

}



This works fine except it returns an error when removing the original items that the duplicates were created from. Any idea what I'm doing wrong or is there a much better way to duplicate items?

----

You're adding the same object multiple times, but you probably want to add copies. This will prevent edits to the originals from affecting the new ones, and will hopefully stop your errors as well. Iterate through the array, copy the elements, and add the copies.

