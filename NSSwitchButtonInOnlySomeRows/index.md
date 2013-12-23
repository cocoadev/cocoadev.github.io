---
layout: page
title: NSSwitchButtonInOnlySomeRows
---

I wish to show some check boxes in some of my NSOutlineView's rows. It is no problem to set an NSButtonCell for the column, but then all the rows get a check box.

How do I set it only for some of the rows? I tried to implement outlineView:willDisplayCell:forTableColumn:item:, but I do not know which button kind I should set, not to mention that my cell seems to be largely unaffected by setButtonType: and even setType:NSNullCellType does not change the appearance of my cells.

----

I did this a while back, though not for an NSOutlineView, but rather for an NSTableView. I would assume that what worked for a table ought to work for an outline view since it's just a subclass of NSTableView.

Anyway, what you need to do is subclass NSTableColumn, and override - (id)dataCellForRow:(int)row . Then make your table use that column.
What I did was to have my subclass forward that method call to my table data source, so I could return a slider cell or checkbox cell according to my needs. 

I imagine there's a better way, but that worked for me.

--ShamylZakariya

----

Ahh, thanks a lot! It's actually better than my previous solution, since my NSTableColumn subclass can be set from within InterfaceBuilder :)

For the records, the code is to be found below -- in InterfaceBuilder select the table column and go the the Custom Class page of the Info Panel and select the SwitchButtonTableColumn class, and in your datasource, implement outlineView:canItemBeDisabled: to return either YES or NO.

**Two more questions: Both ProjectBuilder and iTunes use checkmarks smaller than what the system has to offer. Are these available for non-Apple programmers (perhaps they will in Panther with their new tiny size)? and, iTunes allow the user to toggle the checkmark without the item going active (in PB it does go active, but the file is not loaded, as it is when one performs a normal click), how is this possible? the clickedColumn selector is only valid within my target method, which cannot abort row selection AFAIK.**

----
    
@interface NSObject (SwitchButtonTableColumnDataSource)
- (BOOL)outlineView:(NSOutlineView*)outlineView canItemBeDisabled:(id)item;
@end

@interface SwitchButtonTableColumn : NSTableColumn
{
   NSButtonCell* SwitchButtonCell;
}
@end

@implementation SwitchButtonTableColumn

- (id)switchButtonCell
{
   if(!SwitchButtonCell)
   {
      SwitchButtonCell = [[NSButtonCell alloc] initTextCell:@""];
      [SwitchButtonCell setEditable:YES];
      [SwitchButtonCell setButtonType:NSSwitchButton];
      [SwitchButtonCell setControlSize:NSSmallControlSize];
      [SwitchButtonCell setImagePosition:NSImageOnly];
   }
   return SwitchButtonCell;
}

- (void)dealloc
{
   if(SwitchButtonCell)
      [SwitchButtonCell release];
   [super release];
}

- (id)dataCellForRow:(int)row
{
   id outlineView = [self tableView];
   id item = [outlineView itemAtRow:row];
   id datasource = [outlineView dataSource];
   BOOL canBeDisabled = [datasource outlineView:outlineView canItemBeDisabled:item];
   return canBeDisabled ? [self switchButtonCell] : [self dataCell];
}

@end


----

I can answer your first question, about control size. There are two constants in the documentation for NSCell -- NSRegularControlSize and NSSmallControlSize and you use this by calling [someCell setControlSize: NSSmallControlSize]. 

On the other hand, I have *no* idea how to handle focus changes. I imagine you'll have to do something in the delegate to determine wether the click was on the line itself, or on the cell, and reject or accept selection of the line accordingly.

--ShamylZakariya

