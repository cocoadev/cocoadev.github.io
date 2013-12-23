---
layout: page
title: ValidatingToolbarItems
---

Am I misunderstanding how validateToolbarItem gets called? The items are still connected to actions but the items don't get disabled!! 

    
- (BOOL)validateToolbarItem:(General/NSToolbarItem *)theItem
{
    if (General/findField stringValue] isEqualToString:@""])
      {
	[[NSArray *itemsToDisable = General/[NSArray arrayWithObjects:@"General/FindNext",@"General/FindPrevious",@"General/FindAll",NULL];

	return ![itemsToDisable containsObject:[theItem itemIdentifier]];
      }
    return YES;
}


**General/NSToolbarItem addresses this, namely "...it is sent to the item's target, NOT to the toolbar delegate."**

Moving the method to my items' target got it working.

----

**Validating toolbar items with General/NSFontManager**

I've created a few toolbar items that target a standard General/NSFontManager and I can't validate them. Subclassing didn't seem to work.

To validate these toolbar items I need to get the number of selected rows from a tableView (IB Outlet).

 -- General/GarrettMurray

----

Same problem as the opener. Just create your own controller class and target that from the toolbar, passing messages to General/NSFontManager as appropriate

*solved* --General/GarrettMurray

----

You can use interface validation to change properties of toolbar items on the fly, as it were; see General/ChangeToolbarItemProperties
