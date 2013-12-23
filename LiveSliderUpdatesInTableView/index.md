---
layout: page
title: LiveSliderUpdatesInTableView
---

I've made a sample program in which an NSTableView has two columns -- one has sliders, and the other displays the value of the slider. 

Here's an example if my description isn't clear
http://home.earthlink.net/~zakariya/files/SliderTable.jpg

I'm using a custom NSTableColumn and have overridden the dataCellForRow: method to forward data cell requests to my dataSource, since eventually I'll be using different types of cells on a per row basis, according to need. Right now I'm only using sliders, however.


    
- (id)dataCellForRow:(int)row
{
	if (row >= 0)
	{
		return [self tableView] dataSource] 
			tableView: [self tableView] 
			objectValueForTableColumn: self
			row: row];
	}
	
	return nil;
}


the actual source for my controller and dataSource is as such:
Note that the vars [[ValueID and CellID are global, static NSString pointers.

    
- (void) awakeFromNib
{
	NSTableColumn *column;
	
	//create value display column
	column = [[NSTableColumn alloc] initWithIdentifier: ValueID];
	[column setWidth: 50];
	[column setResizable: NO];
	column headerCell] setStringValue: [[ValueID];
	[tableView addTableColumn: column];

	//create slider column
	column = [[MyTableColumn alloc] initWithIdentifier: CellID];
	[column setWidth: [tableView bounds].size.width - 50];
	[column setResizable: YES];
	column headerCell] setStringValue: [[CellID];
	[tableView addTableColumn: column];
	
	[tableView sizeLastColumnToFit];
		
	//create slider cell
	sliderCell = [[NSSliderCell alloc] init];
	[sliderCell setMinValue: -5];
	[sliderCell setMaxValue: 5];
	[sliderCell setControlSize: NSSmallControlSize];
	[sliderCell setNumberOfTickMarks: 20];
	[sliderCell setContinuous: YES];
	[sliderCell setAction: @selector( onSliderCell: )];
	[sliderCell setTarget: self];

	//now make some fake values
	values = [[NSMutableArray alloc] init];
	int i;
	for (i = 0; i < 10; i++)
        {
		[values addObject: 
                        [NSNumber numberWithFloat: ((float) i) - 5.0]];
        }

}

- (void) dealloc
{
	[values release];
}


/*
Table Data Source Methods
*/

- (int)numberOfRowsInTableView:
	(NSTableView *)aTableView
{
	return [values count];
}

- (id)tableView:
	(NSTableView *)aTableView 
	objectValueForTableColumn:(NSTableColumn *)aTableColumn 
	row:(int)rowIndex
{
	if (rowIndex < 0) return nil;
	
	NSString *identifier = [aTableColumn identifier];
	if ([identifier isEqualToString: ValueID])
	{
		return [values objectAtIndex: rowIndex];
	}
	else if ([identifier isEqualToString: CellID])
	{
		[sliderCell setFloatValue: 
                    values objectAtIndex: rowIndex] floatValue;
		return sliderCell;
	}

	return nil;
}

- (void)tableView:
	(NSTableView *)aTableView 
	setObjectValue:(id)anObject 
	forTableColumn:(NSTableColumn *)aTableColumn 
	row:(int)rowIndex
{
	[values replaceObjectAtIndex: rowIndex withObject: anObject];
	[tableView reloadData];
}

/*
Cell callback
*/

- (void) onSliderCell: (id) sender
{
	NSLog( @"sender: %@;\tslider floatValue: %.2f", sender, 
                [sliderCell floatValue]);
}



Basically, this works. You drag the slider, and when you let go, the values column updates to show the change. BUT, the feedback isn't live. E.g., the NSLog statement in onSliderCell: shows [sliderCell floatValue] as static -- as whatever it was at mousedown. I need live feedback, since these sliders are meant to tune a realtime simulation.

It seems to me that the call to setContinuous on sliderCell should have fixed this, but it doesn't. 

Am I missing something obvious here? 

--ShamylZakariya

___________________________________

I hope this the proper way to respond (new member).

I'm just starting to use tableviews, and one of the things I want to do is very close to your code above.
Question: what does the double bracket notation variable mean? Where does it come from? I have not seen it in objective C.
Possible speedup suggestion: Would KVC bindings, perhaps to an ivar, help?

nnickk

