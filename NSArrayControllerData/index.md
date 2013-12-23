---
layout: page
title: NSArrayControllerData
---



I have a problem with NSArrayController : I have a NSTableView and a NSArrayController that is bound to. There are, for example, 5 values for each NSManagedObject of the NSArrayController. But what I want is to have a 6th column, calculated ,and that depends on the other Objects...

To try to be clear, each object is a financial transaction, with an amount, and I want my NSTableView to print a balance for each transaction, indicating the account balance up to this particular transaction. That is : that balance value depends on the previous transaction and the current transaction amount.

I've tried to add a instance variable in the Transaction Class :

    
+ initialize {
        ...

        NSArray *keys = [NSArray arrayWithObjects: @"amount", nil];
        [self setKeys:keys triggerChangeNotificationsForDependentKey:@"balance"];
        ...
}

+ balance {
        ...
}


But from the balance method I cannot reach the previous Transaction Object. So I think what I want should be implemented in a NSArrayController subclass. 

Does anyone have an idea about it ?

----

Using a controller and bindings for some columns in your table does not prevent you from using a traditional data source for your other columns.

I'd recommend that you create a data source object that has a pointer to the array controller, and calculate your value on-demand inside tableView:objectValueForTableColumn:row: by examining the array controller's arrangedObjects.

----

Many thanks, that worked. I linked a delegate to my tableview, and coded the following method in it :

    
- (id)tableView:(NSTableView *)aTableView objectValueForTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex
{
	if (aTableColumn identifier] isEqualToString:@"balance"])
	{
		[[NSNumber *currentAmount = [transactionArrayController arrangedObjects] objectAtIndex:rowIndex] valueForKey:@"amount"];
		[[NSNumber *previousBalance;
		NSNumber *balance;
		
		if (rowIndex < 0)
			return nil;

		// Balance is first amount
		if (rowIndex == 0)
			balance = currentAmount;
		else {
			previousBalance = [transactionArrayController arrangedObjects] objectAtIndex:rowIndex - 1] valueForKey:@"balance"];
			balance = [[[NSNumber numberWithDouble: [previousBalance doubleValue] + [currentAmount doubleValue]];
		}

		[[[transactionArrayController arrangedObjects] objectAtIndex:rowIndex] setValue:balance forKey:@"balance"];

		return balance;
	}
	
	return nil;
}

