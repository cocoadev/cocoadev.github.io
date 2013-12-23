---
layout: page
title: PopupbuttonCell
---



PopupButtoncell

I  am developing a propertylist editor application and i need to set a popup button cell for booltype.code is something like this in subclass of nstablecolumn.Is it missing something.
 ----
    
- (id)dataCellForRow:(int)inRow
{
	NSTableView  *outlineView;
	PlNode *item;
	NSPopUpButtonCell *cell;
	outlineView=[self tableView];
	if([outlineView isMemberOfClass:[NSOutlineView class]])
	{
		item=[outlineView itemAtRow:inRow];
		if(item indexValue]intValue]==BOOL_TYPE)
		{
			cell=[[[[NSPopUpButtonCell alloc]init];
			[cell addItemsWithTitles:[NSArray arrayWithObjects:@"YES",@"NO",nil]];
			[cell setBordered:NO];
		}
	}
	return cell;
}

----
Thanks in advance
 ----
What is the error you r getting? does it crashes? HowToAskQuestions
 ----
Yes application used get crash.but i just fixed  that one! actually i missed these two lines inside if statement
    
else
		{
			cell=[super dataCellForRow:inRow];
		}

thanks for replay.
 ----

