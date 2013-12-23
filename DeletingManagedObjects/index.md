---
layout: page
title: DeletingManagedObjects
---

I have a master detail view where the detail contains a table of transactions -- the table data is bound to an NSArrayController -- this controller's contentSet is in turn bound to the selection of the master list.  When deleting from the table of transactions with this code:

    
- (IBAction)removeTransaction:(id)sender {
	if ([transactionsTableView numberOfSelectedRows] < 1)
		return;
	
	NSArray *objects = [transactionsController arrangedObjects];
	NSManagedObjectContext *context = [dataSource managedObjectContext]; 
	// Only single selection allowed
	id transaction = [objects objectAtIndex:[transactionsTableView selectedRow]];
	[context deleteObject:transaction];
	[context processPendingChanges];
}


the item disappears in the table (as it should).  However, if I select another item in the master view and then go back to the one I just deleted from, the deleted transaction is still there.  I've checked, and upon deletion the object relationships are removed and the deletion appears successful -- but it's not. The funny thing is that if the transaction has a zero value in one of it's attributes it will successfully delete.

Anybody had issues with objects deleting and then un-deleting themselves?  Thanks for any help.

----



Could this be a confusion between the data used by the table view and that being managed by the array controller? I have a funny feeling that I have seen something like this before. Maybe you want to use the selection as managed by the array controller. As I recall, there is a way to get this, but I'm too busy to do the research for you. Perhaps it's a method named "selection". Hint: I get a lot of hits searching the site on the terms "NSArrayController selection".

----

I checked that the array controller used to display the table is actually having all of it's objects deleted, and in fact it is.  The arrangedObjects method returns an empty list after deletion.  I also tried deleting via the controllers methods, but that didn't work either.

----

I just found this page while researching a problem and it saved me!  Thank you!!  I have a question anyway:

I have a managed object context that I am sharing between many NSDocument instances.  I have an NSPersistentDocument that has its own normal managedObjectContext and also a globalManagedObjectContext that comes from AppController.  Each MyDocument instance contains an NSTreeController bound to the global managed object context, with an NSOutlineView bound to its managed objects.  No predicate on the controller, and 'prepares content automatically' set.  (For the curious, the NSPersistentDocument managed object context relates to document data, the global one relates to an iTunes-like navigation system that is visible on every document window.)

My problem was that any time I needed to delete an object from the global managed object context, Core Data would behave beautifully and delete the managed object, but the tree controller and outline view would be left in some kind of inconsistent state and the app would crash.  It seemed to be the controller crashing but I'm still unclear on what was crashing and why.

I spend 6-8 hours looking through the Apple documentation regarding how to notify your UI of updates to a Core Data managed object context.  There is almost no documentation on that because apparently you're supposed to use the removeObject: method in the controller as opposed to deleting objects directly out of the managed object context.  I attemped all kinds of things to get the controller and the view to update after deleting an object.  I tried reloadData: on the view, fetch: on the controller.  I tried un-checking 'automatically prepares content' and manually setting the controller's contents with setContent: using the results of a manual Core Data fetch operation.  Nothing worked.  Several of my workaround attempts broke things like selection preservation.

I came here and noticed the call to NSManagedObjectContext:processPendingChanges after the call to NSManagedObjectContext:deleteObject.  I just tried that and hey wow it works!  Everything works perfectly now and it's all fully automatic.  I don't have to send any notifications or do any manual Core Data fetches or anything.  Absolutely beautiful.

I still don't know WTF is going on.  Does processPendingChanges invoke some kind of KVO magic that informs the controller to re-fetch and then the view updates in turn?  I was just about to attempt to wire my project up to send willDoSomethingOrAnother KVO notifications to the controller as it did the delete operation.  Is that possibly the magic that processPendingChanges is doing when I call it, or am I completely off?

There is very little documentation or discussion on using Core Data outside of the prescribed routes illustrated in the Core Data XCode templates.  Even for something simple like my shared global managed object context.  I don't think that what I'm doing is that outrageously complex and I think that Apple could provide better documentation on things like how to update UI in response to model changes.  The existing documentation assumes that updates to the model will always happen through controllers, and I couldn't find any bread crumbs to lead me to the NSManagedObjectContext:processPendingChanges: method when I needed it.

----

processPendingChanges does nothing magic. When you delete an object it is not deleted until save: occurs. AFAIK, processPendingChanges is done at least once per runloop cycle. Have you tried to actually save after the delete ?

