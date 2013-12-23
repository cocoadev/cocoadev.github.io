---
layout: page
title: CoreDataBindingsProblems
---

I'm getting a lot of runtime errors that don't show up anywhere on Google and are hardly descriptive. Yay for no compile-time checking! So I'm jotting them down here (and hopefully including solutions) so that they'll be searchable and people will know they're not the only ones having these problems. Or maybe I really am the only one who can't get CoreData working right..

Apologies in advance for the excessive sarcasm.

-Dave

----

***Illegal container for relationship***

  ..something to do with bindings and key paths.

----
It is in effect that one managedObject has a one-to-many relationship with another managedObject, but that relationship is being filled with an array (eg) instead of a set.  This error shows up when the context tries to save its state.  Anyone experiencing this problem is advised to look for methods in their objects which, when called, return traditional data collection objects or expect to receive them.  This is obligated because the Core Data model typically uses NSSet to manage its data, whereas everyone using cocoa prior to Core Data only used NSArray or NSDictionary.  Setting relationships (or custom accessor methods) with incorrect return types means that when the MOC tries to save itself, it will legitimately complain that "This NSArray stuff is an illegal container for this NSSet relationship".

In my case the solution was that as I was migrating from an existing data model, I had to make important conversions in many minor points of my code.  I had two change two places, specifically (your code may have more points of misalignment between your old model and the new NSSet model.

1) Previously, I had a controller SDLibraryController : NSObject.  By changing this to SDLibraryController : NSManagedObject, I had to comply with what the MODEL was expecting of my code.  So for my attribute named "Clip" (a to-many relationship pointing to another MO),

    
- (void)outlineViewSelectionDidChange:(NSNotification *)notification
{
	[[[NSApp delegate] valueForKey:@"arrayController"] setContent:
              [[notification object] selectedItems] valueForKey:@"observedObject"] 
              valueForKeyPath:@"@distinctUnionOfArrays.clips";
}


(oh, and sorry but SelectedItems is a simple category method on NSOutlineView which runs repeatedly through itemForRow: for every id in selectedRows).  This becomes (and read carefully is is a microscopic change):

    
- (void)outlineViewSelectionDidChange:(NSNotification *)notification
{
	[[[NSApp delegate] valueForKey:@"arrayController"] setContent:
               [[notification object] selectedItems] valueForKey:@"observedObject"] 
               valueForKeyPath:@"@distinctUnionOfSets.clips";
}


2) I had to completely kill my custom accessor method, which returned an NSArray.  Now I just set the controller's content internally with (generic code):

    
	NSMutableSet *toManyRelationshipSet = 
              [self mutableSetValueForKey:@"toManyRelationshipKey"];
		[toManyRelationshipSet addObject:newRelation];


and I leave the NSManagedObject to deal with the get accessor.  Which is one of the reasons of migrating in the first place.
-Stephen
----

***Cannot perform operation if childrenKeyPath is nil***

Oh, yeah! I forgot to set childrenKeyPath in my NSTreeController!

***Cannot create NSArray from object <_NSFaultingMutableSet: 0x3361d0> () of class _NSFaultingMutableSet - consider using contentSet binding instead of contentArray binding***

Uh, okay, I'll change them. Now I get this:

***Cannot create NSSet from object <_NSControllerObjectProxy: 0x385440> of class _NSControllerObjectProxy***

Ugh.

Okay, some configuration of contentArray and contentSet has stopped the warnings. But I have no idea how or why. I'm tempted to complain about how everything was so much easier when we could do everything in code, or at least it made sense, and the compiler could help us out with useful warnings and errors, but I also remember some big hurdles figuring out AppKit.
---
Bind NSArrayController to the NSTreeController by setting the contentArray of the NSArrayController to selectionIndexPaths, and not selection.


***Nothing's showing up in my table view***

Remember to set binding for managedObjectContext in your controller objects!

***selectedObjects isn't empty when nothing's selected in my table view***

Uncheck the "Avoids empty selection" checkbox in the array/tree controller's attributes

***Unchecked "Avoids empty selection", and now selectedObjects is always empty, even with rows selected***

Binding selectionIndexes in the NSTableView**'s to selectionIndexes in the controllers fixed this

***[<_NSControllerTreeProxy 0x393640> valueForUndefinedKey:]: this class is not key value coding-compliant for the key path.***

NSOutlineView's content bound to NSTreeController's arrangedObjects.path, NSTreeController's contentSet bounds to NSArrayController's selection.files

Ohh, right--I have to bind the table column, not the outline view

***Exception raised during posting of notification.  Ignored.  exception: cannot find data for a temporary oid***

What the heck is an oid? --JediKnil

Object ID maybe? -- does anyone know what cuases this problem????

Definitely Object ID.  What it means by "temporary oid" is that if a document is created but not yet saved, the OIDs are going to change the first time it is saved, from temporary to their new permanent ones - this is the only situation in which an OID will ever, ever, ever change, so it's not surprising if some code somewhere makes the assumption that OIDs will never, never, never change.  Hope that helps you track it down. -- DanKnapp

----

***Unacceptable type of value for attribute***
Example: Unacceptable type of value for attribute: property = "name"; desired type = NSString; given type = NSCFBoolean; value = 1.

It sounds like you have an NSOutlineView bound to an NSTreeController.  I bet that this outlineView has custom datacells populating it.  I'd also venture to guess that those cells are a subclass of NSButtonCell.  If I were cornered, I might even say that you see this error when trying to enable editing on those cells, and this is what you see when ending editing of the cell's text.  Left with no other option, I'd gingerly remind any victim of this error message that buttons report their values as booleans, and that obviously there is nothing wrong with your managed object context or your binding, or your outlineview's implementation (or lack thereof) of the myriad delegate methods declared in NSText et al, or even the default editing layout provided by the window, but that more likely you need to add two methods to your cell, which the editor uses as its means of interacting with the cell:

    
- (id)objectValue
- (void)setObjectValue:(id)anObject


I leave the details to you!
-Stephen

----
An extension of this is the slightly misleading error message you get when adding a dodgy NSSet to a to-many relationship.
    
Unacceptable type of value in to-many relationship: property = "cmap"; problem = <NSCFSet: 0x18b1f770> 
...
desired type = NSManagedObject; given type = NSCFSet; value = {...}.


Notice it is saying that the desired type is NSManagedObject not a NSSet because it is referring to the values inside the NSSet
and not the NSSet itself which IMHO is not that clear from the error message. 
In this case it so happens I was calling the wrong function, and the NSSet was a set of NSDictionary<nowiki/>s and not NSManagedObject<nowiki/>s. 
I've also had this when the set had not been allocated, 
----

I've got yet another variation on this.
    
Unacceptable type of value for to-one relationship: property = "parent"; desired type = BinderItem_BinderItem_; given type = NSManagedObject; .....


But what's confusing here is that it goes on to show that the NSManagedObject belongs to the entity "Knowledge", which is a subentity of "BinderItem", so it ought to work!  My only guess is that somehow the object was created with the wrong isa pointer, but why would that be and what can I do about it?  And no, neither object here is a fault, I checked.

Some time later, I eventually fixed things by regenerating all the .h files using the wizard, even though there was no reason to think they were out of date.  The only substantial change that seems to have happened is that it now declares the properties as being BinderItem instead of NSManagedObject, perhaps because the last time I generated the files was before I added BinderItem as an abstract base entity...

My guess is that google has so little information on this because people aren't sure exactly how they solved it.  Good version control enabled me to tell what had changed.  So I'm documenting this here for others' benefit.

-- Dan Knapp

----
There is another variation on this that has to do with using IB to set the content of one array controller by binding the contentArray of another.
I have an NSManagedObject called AnalysisGroup. It has a to-many relationship with another NSManagedObject called AccountPaymentSchedules.
The content container of a to-many relationship is an NSSet, therefore if I want to bind AccountPaymentSchedules content to selection.AnalysisGroup, I must bind it to contentSet. I have seen dated documentation that has this bound to contentArray. This of course will throw an error similar to the one posted earlier.

I found some documentation on Core Data Programming Guide under troubleshooting: http://developer.apple.com/mac/library/documentation/cocoa/Conceptual/CoreData/Articles/cdTroubleshooting.html
Close to the end of that page, there is a topic "A table view bound to an array controller doesn't display the contents of a relationship" which details the behavior and the solution.

Hope this is useful.

-- Will DeShazer
----
I just had a similar error to the "Unacceptable type of value in to-many relationship" one above. My code was copying objects from one Core Data stack to another, and while the objects I was initializing were created in the correct managed object context, it turns out that the NSEntityDescription I was using wasn't the one from the NSPersistentStoreCoordinator that I was copying *to*, but rather the one I was copying *from*. Both store coordinators were using the same .xcdatamodel file, but apparently each has its own NSEntityDescription instances, and even though the entity descriptions are identical, Core Data seems to get very confused if you use an instance from a different stack. 

-- Brian Webster

I also had a similar error to the "Unacceptable type of value in to-one relationship" one above, pretty similar to what Dan saw. The root cause is I didn't set the correct class name in the object model. If you click the Entity, there are three fields Name, Class and Parent. Somehow, the Class field was set to "NSManagedObject" instead of "MySubClass".

-- Ji Fang

