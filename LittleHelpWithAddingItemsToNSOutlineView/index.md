---
layout: page
title: LittleHelpWithAddingItemsToNSOutlineView
---

Ok, before you all jump down my throat and say that adding items into an NSOutlineView is easy, let me explain my situation.  I'm trying to add an item into a selected folder in the outline view.  i.e. I have 3 nested folders and I'm currently selecting an item in the third folder.  When I click 'Add Item' I want an item to be added into the third folder.  I tried doing this and sort of got it to work.  The problem is the new item gets added to every folder that the nested folder is in (in other words, it creates three new items).  Here's my code:

    
- (void)addNoteObject:(NSMutableDictionary*)aKey intoArray:(NSMutableArray*)anArray {
    NSMutableArray* children;
    unsigned i;
    
    for(i = [anArray count]; i >= 0; )
    {
       id item = [anArray objectAtIndex:--i];
       if(children = [item objectForKey:@"Children"])
            [self addNoteObject:aKey intoArray:children];
       if([item isEqualTo:[noteBoxView itemAtRow:[noteBoxView selectedRow]]] ) {
            [anArray addObject:aKey];
        }
    }
}


Any help would be greatly appreciated!

Cheers, DanielHoward

----

I think your problem is you are modifying your data source while you are calling itemAtRow. The �itemAtRow� will become a moving target if you add items before calling �reloadData�. You probably shouldn�t make this method call itself recursively if you intension is to only add the item to a single group (which should always be the case unless you implement some form of leaf links). 

----

If you're trying to find the parent of an item in an outline view, don't search the whole data source for it.  Either provide a parent link in your child items  or use -levelForRow: while decrementing the row number until you find a row with one less level than the item you're looking at.  Assuming you're not interested in the first, here's a quick (entirely untested, not even compiled) category on NSOutlineView demonstrating how to do the second.
    
@implementation NSOutlineView (ParentItemSearch)
- (id)parentOfSelectedRow
{
	int currentRow = [self selectedRow];
	if (currentRow == -1) { // i.e. no selected row
		return nil;
	}
	int currentRowLevel = [self levelForRow:currentRow];
	if (currentRowLevel == 0) {
		// i.e. it's at the top level
		return nil;
	}
	// just decrement the row number until the level number decrements
	while ([self levelForRow:--currentRow] >= currentRowLevel) { }
	return [self itemAtRow:currentRow];
}
@end

Best of luck.  -- Bo

----
Did you ever run into an NSOutlineView (or NSTableView for that matter) that refuses to end editing? When I double click on an NSTextFieldCell in my outlineview, I can edit it, an the value is set when I hit Enter. But if I try to do it a second time, the cell sometimes refuses to get out of editingmode: it stays highlighted. Now if I doubleclick on another TextFieldCell, the *same* editor is preserved, which means the value I typed previously is copied over to my new cell right away.
Any ideas?

Thanks -- Yannick

