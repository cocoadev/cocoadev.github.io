---
layout: page
title: DynamicOutlineComboBoxCell
---



What I am trying to achieve:
----
I want to use an NSComboBoxCell on some rows (not all) of some columns in an NSOutlineView.  How I decide is based on the data.  Imagine you have machines that you can configure.  Some machines have a toggle state for which "yes" and "no" are good values, but maybe some other values make sense.  In the "Toggle State" column, for the machines where it makes sense to have a ComboBox, I want the user to get an Combo Box when they try and edit the state.  When they are not editing, they should just see the state, not the combo box arrows.

How I am going about it:
----
I sub-class NSTableColumn and my sub-class looks like this:
    
@interface tableColumnComboBoxCell : NSTableColumn 
{
	NSComboBoxCell	*comboBoxCell ;
	int				editRow ;
}
- (id)initWithIdentifier:(id)anObject ;
- (void)dealloc ;
- (void)endEdit:(id)sender ;
- (void)setEditRow:(int)row ;
- (id)dataCellForRow:(int)row ;
@end ;


What I am really interested in, is providing my own cell when it comes to editing that row.  That's why I provide my own dataCellForRow which simply looks like this:
    
- (id)dataCellForRow:(int)row
{
	if( editRow == row )
		return comboBoxCell ;
	return [super dataCellForRow:row] ;
}


I create the "Toggle State" column in the OutlineView programatically and it is created as a tableColumnComboBoxCell.

In the delegate method 
    
- (BOOL)outlineView:(NSOutlineView *)outlineView shouldEditTableColumn:(NSTableColumn *)tableColumn item:(id)item
I see when one of my special "items" is being considered, and then I do the following:
	[((tableColumnComboBoxCell*)tableColumn) setEditRow:tableColumn tableView] selectedRow ;
	[outlineView reloadItem:item] ;


This works!  The reload is needed to force a refresh so my ComboBoxCell can become the cell.  Otherwise you need to click one more time to start editing.

So, what's my problem?
----
I have several problems with this solution.

*1) The NSComboBoxCell is of the kind you would get stand-alone, not in a table row.  By this I mean it has the blue tag on the side, not the small little up and down arrows that you get if you dropped the NSComboBoxCell on a Table/Outline view.
*2) Where I set the editRow, I am asking what's the selectedRow.  This isn't always correct.  If I tab out of one row, into a comb-box row, the selectedRow (at this stage) is still the row I am tabbing from.  My CombBox now appears on the wrong row!  Using editedRow doesn't help either (and may not even work at all?).
*3) I can't quite figure out how to know when editing has finished.  I set the target & action method and I just terminate editing in there.  That means that if the user selects a value, they are done editing, whether they like it or not!  Works, but isn't ideal.  Also, the user can get out of the edit without me knowing, leaving the NSComboBoxCell hanging there.  Not at all what I want.


The question (finally) - How should I REALLY do this?
----
----
Change the line in ShouldEditTableColumn to the below (just ask the table what row the item is on (obviously) helps! ;)
    
- (BOOL)outlineView:(NSOutlineView *)outlineView shouldEditTableColumn:(NSTableColumn *)tableColumn item:(id)item
...
	[((tableColumnWithComboBoxCell*)tableColumn) setEditRow:[outlineView rowForItem:item]] ;
...

Item 2 is now taken care of.  I tried sub-classing the NSComboBoxCell and then tried to tamper with _buttonCell but I don't really know nor can I see how the buttonCell knows what "control" it should use.  I also would like the popup box to appear automatically, but I don't know how to do that either.  Sending a click didn't quite work (dataCellForRow is called repeatedly so doing a click in there doesn't give the desired result),  and sending _popUp a makeKeyWindow message didn't work either.  That did set isVisible to TRUE but it didn't actually show up.
----

