---
layout: page
title: TableViewRowTextColor
---

I'm working on a project that has a General/NSTableView with a list of items (everything's in General/CoreData and Bindings). The items have a "date due" attribute that I want to alter the color of the row when the current date is near and/or past that date. I'm guessing it would probably change the color as the table popluates from the Array Controller. I know I can just do it with an if-else statement, but I can't seem to find what to subclass. I tried subclassing General/NSTableView, but didn't seem to find anything. I know this question has probably been asked a billion times, but I can't seem to find a good solid answer. I've seen it in a few good apps and hope it's easier to implement that it seems. Thanks, --General/LoganCollins

----

Use the method tableView:willDisplayCell:forTableColumn:row: in your table view delegate, and then you can call     [cell setTextColor:General/[NSColor redColor]] or whatever.

----

Now that I know that, does anyone know how I would go about querying the General/CoreData information? Should I connect the array controller to the delegate and just use that, or is there a better way? With that said, I tried changing the color without using an if statement and it gave me a runtime error about General/NSButtonCell. My first column is a checkbox column for "completed". Is there a good way around this? --General/LoganCollins

----

Because     tableView:willDisplayCell:forTableColumn:row: is called *after* the cell gets its data from the table's data source or array controller, just check the cell's     stringValue,     objectValue, or     representedObject, which can be set in the data source's     tableView:objectValueForTableColumn:row: method (if you are using bindings,     representedObject won't be that useful, but the other two might). This way you don't even have to bother with checking General/CoreData. --General/JediKnil

----

Okay, I implemented it just as you said, but that method (    tableView:willDisplayCell:forTableColumn:row:) just returns nil. Am I missing something? Do I have to implement that method myself?

Here's my code for the tableview's delegate method:

    
- (void)tableView:(General/NSTableView *)aTableView willDisplayCell:(id)aCell forTableColumn:(General/NSTableColumn *)aTableColumn row:(int)rowIndex {

	id dataSource = [aTableView dataSource];

	General/NSTableColumn *completedColumn = [aTableView tableColumnWithIdentifier:@"Completed"];

	if (aTableColumn != [aTableView tableColumnWithIdentifier:@"Completed"]) {
		if ([dataSource tableView:aTableView objectValueForTableColumn:completedColumn row:rowIndex] == YES) {
		
			[aCell setTextColor:General/[NSColor greenColor]];
		
		} else {
		
			[aCell setTextColor:General/[NSColor blackColor]];
		
		}
	}
	
}


Thanks, --General/LoganCollins

*Um, I would guess you meant     tableView:objectValueForTableColumn:row:, but that's not the real issue here. By this point I'll assume you're using General/CocoaBindings, so you don't need to worry about that. My point was that the value of     aCell will already be set by this point, so you can do something like this (adapt to your situation): --General/JediKnil*
    
- (void)tableView:(General/NSTableView *)aTableView willDisplayCell:(id)aCell forTableColumn:(General/NSTableColumn *)aTableColumn row:(int)rowIndex {
	if (General/aTableColumn identifier] isEqualToString:@"Completed"]) {
		if ([aCell intValue] != 0) { // if it is YES
			[aCell setTextColor:[[[NSColor greenColor]];
		
		} else {
			[aCell setTextColor:General/[NSColor blackColor]];
		}
	}
}


----

That doesn't seem to do anything. It looks to me that that code would only change the text color for the completed column, which doesn't contain any text. It contains a switch button. I need it to change for the entire row. Am I just missing what your talking about? I'm not very experienced with table delegates, as you can probably tell. --General/LoganCollins

----

Are you wanting all the text forecolor in the entire row to change to a certain color? If so, don't check for the table column's identifier, just use the rowIndex value.

----

Let me restate my problem a little better. What I have is a tableview with several columns, the first of which is a checkbox cell column. The last holds a date. What I want to do is be able to change the color of the entire row for a record when a certain criteria is met, i.e. the checkbox is checked or the date is today. How would be the best way to implement this? I use General/CoreData and General/CocoaBindings. There, maybe that will help to clarify this a little. :) --General/LoganCollins

*You have to be more specific. Changing the color of the entire row could mean the background color, the foreground color, the highligh color, the alternating row color, etc etc.*

The text color, specifically.

----

This is easy stuff...

    
- (void)tableView:(General/NSTableView *)aTableView willDisplayCell:(id)aCell forTableColumn:(General/NSTableColumn *)aTableColumn row:(int)rowIndex {
   if ([aCell respondsToSelector:@selector(setTextColor:)]) // we can change the text color
     if (rowIndex == aRowYouWantColored) [aCell setTextColor:General/[NSColor greenColor]];
     else [aCell setTextColor:General/[NSColor blackColor]];
}


How you determine which rows need colored and which ones don't is up to you.

--- 

A more efficient way to set the text color (if that's all you want to do) is use NSAttributedString when giving the object value during the table load, rather than every time a cell view is updated.  Here is an example:

    <pre>
- (id)tableView:(NSTableView *)tView objectValueForTableColumn:(NSTableColumn *)tableColumn row:(int)row {

	NSMutableDictionary *attributes = [[NSMutableDictionary alloc] init];
	//[attributes setObject:[NSFont systemFontOfSize:11] forKey:NSFontAttributeName];
	[attributes setObject:[NSColor colorWithCalibratedRed:0.000 green:1.000 blue:0.000 alpha:1.000] forKey:NSForegroundColorAttributeName];
		
	
	if ([[tableColumn identifier] isEqualToString:@"favoriteColumn"]) {
		return [[NSAttributedString alloc] initWithString:@"your string here" attributes:attributes];
	} else {
		return @"---";
	}
}
</pre>
-- mhfadams at gmail dot com
