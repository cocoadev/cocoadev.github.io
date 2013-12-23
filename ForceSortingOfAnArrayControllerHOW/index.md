---
layout: page
title: ForceSortingOfAnArrayControllerHOW
---

How do I tell an NSArrayController to sort one of its associated NSTable columns in descending order without forcing the user to manually click on the column header? I still want the user to be able to sort the data, but at one point of operation in my application I want to rearrange the table for the user. http://goo.gl/OeSCu

Thanks,
Kent!

----

It seems that the array controller's     -setSortDescriptors: is what you're looking for.  Some code:
    
NSSortDescriptor* aSortDesc = [[NSSortDescriptor alloc] 
                                                 initWithKey:[tableColumn identifier] ascending:NO];
[arrayController setSortDescriptors:[NSArray arrayWithObject: aSortDesc]];
[aSortDesc release];

Substituting your respective objects for the tableColumn and arrayController variables of course. You may have to call
    -rearrangeObjects afterwards to get the changes to show, but I think that's the ticket.  -- Bo

----

Slightly related question: is there any way I can get the     rearrangeObjects invoked when the sort descriptor is changed for one of the objects?

My array controller is bound to File's Owner with a key path of     model.events, model being a pointer to the actual model and events being an array of events with a     date key containing an NSDate object, and the sort descriptor has     date as the sort key.

The model implements the various ...InEvents... accessor methods, so the array controller will register itself as an observer of the     events key of the model object, and thus will update the table view when objects are inserted,  removed or even changed � but it will not re-arrange.

Perhaps I should enforce the policy that no-one must change an event object, but only replace it? This just seems like unnecessary overhead?

*Later...*

It seems that the array controller will re-arrange its objects if I invoke:
    
[self willChange:NSKeyValueChangeSetting valuesAtIndexes:[NSIndexSet indexSetWithIndex:i] forKey:@"events"];
...
[self didChange:NSKeyValueChangeSetting valuesAtIndexes:[NSIndexSet indexSetWithIndex:i] forKey:@"events"];

But these methods are for manual implementation of key-value coding/observation � but they seem not to be invoked by default?

The way I change the event objects is by getting the object and invoking setObject:forKey: on it. Is there any other way I can set the new value so that it will trigger a notification (I did try setValue:forKey: instead)?

It's a little odd, because the table column does update when I change the date, so somehow a notification is sent. Perhaps the table column adds itself as an oberver of the key of each individual object in the array!?!

----

Maybe it would be possible to return a proxy (from the to-many accessor methods) which would issue the proper will/didChange:NSKeyValueChangeSetting when an attempt to mutate the object is made. I think this is similar to how the run-time system already handles notifications. I.e. when something adds an observer to an object, the object in question is (run time) "replaced" (somehow) with a proxy which invokes the will/didChangeValueForKey:. But how the runtime actually manages this trick is beyond my knowledge -- anyone has an idea?

----

I'm a little confused as to what to use in place of the two variables you've mentioned (I'm new to this Cocoa stuff).  My actual NSArrayController object is called "combatantController" so I've done this for line 2:

    
[combatantController setSortDescriptors:[NSArray arrayWithObject: aSortDesc]];



But, in line 1, what do I put in place of this?:

    
[tableColumn identifier]


The column that I want to sort by is has the identifier "Initiative".  What is the tableColumn object here, though?  I assume there has to be an object for this table column (from the code snippet) but how do I make something point to my particular column?

Or am I simply missing something here?

Thanks,
Kent!

----

Sorry 'bout that.  I'm still thinking of  the old school technique for binding columns to a data source, not the new controller-based stuff.  What you want     [tableColumn identifier] to be is the key on which you want to sort the objects in your array controller.  So, for example, if the objects in your array each have an 'initiative' variable which you want your array to be sorted in decreasing value of, you'd allocate your sort descriptor like so:     [[NSSortDescriptor alloc] initWithKey:@"initiative" ascending:NO];  -- Bo

----

Thanks, Bo!  This worked GREAT!

Kent!

----

Kindof hacky, but this is what worked for my app if you want the array controller to resort itself without jumping to the currently selected row.
    
- (void) secretRearrangeObjects {
	[self _setObjects:[self _sortObjects:[self arrangeObjects:[self objects]]]];
	[YourAwesomeTableReference reloadData];
}


And of course your category additions to get rid of compile errors:
    
@interface NSArrayController (ASAdditions)
- (NSArray *) objects;
- (id) _sortObjects:(id)objects;
- (void) _setObjects:(id)objects;
@end

@implementation NSArrayController (ASAdditions)
- (NSArray *) objects {
	return _objects;
}
@end


-MichaelBianco

