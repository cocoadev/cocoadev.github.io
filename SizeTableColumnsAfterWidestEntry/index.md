---
layout: page
title: SizeTableColumnsAfterWidestEntry
---



Is there any way I can setup my table view (or outline view) to have its column be as wide as the widest entry (or as wide as available space, if the widest entry is smaller than available space)?

----

I have some pseudo code that should help you get started. I'd have some actual code, but I've only been awake for 10 minutes, and the coffee isn't ready yet! So here goes with the theoretical stuff:


*Go through each item in the table view (probably best to do 
*Determine if this is the longest line encountered so far. With text, this is easy: just compare the two string's length's.
*If it is, using the metrics from NSFont, get the width of the string. This can be done with NSFont's widthOfString: method. Be sure to use the same font as the table view uses for this!
*Don't forget to add in the cell's horizontal padding! I think NSCell has some method for this, or look through Apple's sample code for the icon and text cell thing.
*Store this width somewhere.
*When you are done going through the items, check the stored width against the minimum width you want.
*Set the desired column's width to the larger of the two from above.


That should work in theory (as should many things), but let us know if you get some code fleshed out!

- QuentinHill

----

No, this is too complicated and error prone. Consider "ww" will often be wider than "�". Best to let the table columns cell do the work for you. Here's an example, where the data source is an array of dictionaries, and each item in the dictionary is looked up by table column identifier:

    
NSCell *cell;
NSEnumerator *e;
NSDictionary *d;
NSString *key;
NSString *identifier = [tableColumn identifier];
float width;

// Measure the width of the column identifier.
width = tableColumn headerCell] cellSize].width;

// Measure the width of each data item. Keep the longest width.
cell = [tableColumn dataCell];
e = [dataSourceArray objectEnumerator];
while (d = [e nextObject]) {
    float tmp;
    
    key = [d objectForKey:identifier];
    if (!key) continue;
    
    [cell setStringValue:key];
    tmp = [cell cellSize].width;
    width = (tmp > width)? tmp : width;
}

// Add a small fudge factor just to be safe
width += 5.0;

// Set the table column width
[tableColumn setMinWidth:width];
[tableColumn setWidth:width];


-- [[MikeTrent

----

What's with today's generation to do everything efficiently and safely? What's with my constant desire to find the hardest way to do something?

-- QuentinHill

----

Actually, in school we spent some time working on maximally inefficient algorithms. Like a search routine that will check every element in a list before it finds the one you're looking for ("It's always in the last place you look!") and such. The hard part was making sure you're doing constructive work ("making the searching set smaller!") rather than specious work ("measuring how many bytes it consumes"). 

-- MikeTrent


----

I then just need to rewire my programming brain to ask "what's the easiest way" when I approach things.

And to verify a question I was going to ask (and bring this back on topic), cells do track the smallest size needed to display their content! From the docs:

"cellSize: Returns the minimum size needed to display the receiver, taking account of the size of the image or text within a certain offset determined by border type."

I now feel confident in naming MikeTrent the victor in the "will work, and work fast and small" situation. But if you are looking for a complex, error-prone method, mine can't be beat :P

-- QuentinHill

----

Adapted from Mike's approach for a table using Cocoa Bindings (NSArrayController). In IB, make sure to set the NSTableColumn identifier to the key you're binding on. It seems to work, but feel free to improve if I did something stupid, which is very very improbable. ;)

--Theo

    
- (void)resizeColumnsToWidestEntryForTable:(NSTableView *)aTable arrayController:(NSArrayController *)anArrayController
{
	NSEnumerator *columnEnum = aTable tableColumns] objectEnumerator];
	[[NSTableColumn *tableColumn;
	while(tableColumn = [columnEnum nextObject])
	{
		BOOL skipColumn = NO;
	
		// measure the width of the column identifier.
		float maxWidth = tableColumn headerCell] cellSize].width;

		// measure the width of each data item. Keep the longest width.
		[[NSCell *cell = [tableColumn dataCell];
	
		NSEnumerator *objectEnum = anArrayController arrangedObjects] objectEnumerator];
		id anObject;
		while(anObject = [objectEnum nextObject])
		{
			id colObject = [anObject valueForKey:[tableColumn identifier;
			
			// skip column if it doesn't contain a string
			if(![colObject isKindOfClass:[NSString class]])
			{
				skipColumn = YES;
				break;
			}
			
			[cell setStringValue:colObject];
			float width = [cell cellSize].width;
			maxWidth = (width > maxWidth) ? width : maxWidth;
		}
		
		// add small fudge factor to be safe
		maxWidth += 10.0;

		if(!skipColumn)
		{
			// set the table column width
			[tableColumn setMinWidth:maxWidth];
			[tableColumn setWidth:maxWidth];		
		}
	}
}


----
This approach can be VERY slow. Maybe it's okay for small amounts of data, or if you never need to resize the columns. 

----
It also does not consider cell formatters.

----
So, I'm looking at all this code, it's great but.. couldn't that be done automatically through binding column width to something? I'm very surprised that you have to go through all this just to do something so simple and useful.

