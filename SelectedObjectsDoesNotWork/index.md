---
layout: page
title: SelectedObjectsDoesNotWork
---

Hi. I just got BookCocoaProgMacOSX and have reached Chapter 12. I am trying to implement the custom alert panel features, but the program repeatily claims that none of the items in the table view are selected, even though I have Mutliple Selection on. And the odd thing: **the program does not crash**, Xcode doesn't spit out a warning or error and Interface Builder validated the interface! Here is the remove: code:

    
- (void)remove:(id)sender
{
	NSArray *selectedPeople;
	int choice;
	
	NSLog(@"entered removal.");
	selectedPeople = [personController selectedObjects];
	if (selectedPeople == nil) {
		NSLog(@"selectedPeople == nil?? but how?");
		return;
	}
	choice = NSRunAlertPanel(@"Delete", @"Do you really want to delete %d %@?", @"Delete", @"Cancel", nil,
	                         [selectedPeople count], (([selectedPeople count] == 1) ? @"record" : @"records"));
	// without the above if, "Do you really want to delete 0 records?" would appear
	if (choice == NSAlertDefaultReturn)
		[personController remove:sender];
}


Am I doing anything wrong with this function, or is this not enough to diagnose? - PietroGagliardi

----
This might be a simple question, but are you sure your outlet 'personController' is bound to the controller?
----
That fixed it. Thanks.

I think I overlooked something in the earlier chapters :-)

----

Also consider that you may be handed back an empty array. i.e., not 'nil', but an empty NSArray instance.

