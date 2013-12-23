---
layout: page
title: CoreDataNSArrayControllerPopUpMenu
---



I am creating a little program that has this data structure:

Client {name, arrayOfTasks}  //name is string, array of tasks

Task {name, arrayOfTimeStamps}  //name is string, array of timestamps

timestamps{starttime, stoptime}  //nsdate type
}

That is the basic structure:  Clients have tasks, tasks have timestamps.
Everything works great when I use arraycontrollers with tables and bindings.
What I've tried to do is bind the same arraycontrollers with popupmenus instead of tableviews.  

Client Popup -> (after selection) Task Popup -> Display TimeStamps in a Table 

Seems simple enough to me, but the popupmenus don't generate the proper updating, so after selecting a client from the client popup the task popup is not updated.

I thought I could implement a custom controller, which I can't figure out what to override.  

I also tried using outlets, so I am receiving from the popup, but I am ignorant to how to access my information in core data.  I first tried this in the popupaction

    
- (IBAction)updateClient:(id)sender //this is the action for popupmenu->client 
{
	NSArray *ownerArray = [clientController selectedObjects]; //IBOutlet for a clientController in my NIB
	NSMutableSet *clients = [ownerArray mutableSetValueForKey:@"clients"];  //
//	NSManagedObjectModel *clients = ownerArray mutableSetValueForKey:@"clients"] anyObject];
	[[NSLog(@"Clients set - %@",clients);
	NSEnumerator *clientEnumerator = [clients objectEnumerator];
	id anObject;
	[clientPopUp removeAllItems];
	while (anObject = [clientEnumerator nextObject])
	{
		NSLog(@"anObject %@", anObject);
		NSLog(@"anObjectKey %@",[anObject mutableSetValueForKey:@"name"]);
		[clientPopUp addItemsWithTitles:anObject mutableSetValueForKey:@"name"] allObjects;
	}
}

But wow that is ugly, and though it did work, it was very clunky and I didn't understand why it worked.  So I am setting out to find the right answer.  I think part of it is using the managedObjectContext, but how??  How do I as managedObjectContext 

	managedObjectContext = [[NSApp delegate] managedObjectContext];

Thanks for any help.

