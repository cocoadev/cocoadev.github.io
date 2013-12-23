---
layout: page
title: UITable
---



Part of the iPhone General/UIKit framework. Subclass of General/UIScroller.

See also: General/UIPreferencesTable and General/UISectionList.

**Methods**

<code>- (id)initWithFrame:(General/CGRect)frame;</code>

<code>- (void)addTableColumn:(General/UITableColumn*)column;</code>

<code>- (void)setDataSource:(id)dataSource;</code>

<code>- (void)setDelegate:(id)delegate;</code>

<code>- (void)setSeparatorStyle:(int)style</code>

General/SeparatorStyles are: 1 for a 1px grey line, 2 for a 2px grey line, 3 (default) for no separator lines.

<code>- (void)reloadData;</code>

you can deselect the select row by calling the following at the end of the tableRowSelected callback:

	<code>General/[notification object]cellAtRow:[[notification object]selectedRow]column:0] setSelected:FALSE withFade:TRUE];</code>

[[EcumeDesJours

Unlike in General/AppKit, you must call reloadData for the table rows to appear.

----

**
Data source methods
**

<code>- (int)numberOfRowsInTable:(General/UITable*)table;</code>

<code>- (General/UITableCell*)table:(General/UITable*)table cellForRow:(int)row column:(General/UITableColumn *)column;</code>

<code>- (General/UITableCell*)table:(General/UITable*)table cellForRow:(int)row column:(General/UITableColumn *)column reusing:(BOOL)flag;</code>

----

**
Delegate methods
**

<code>- (BOOL)table:(General/UITable *)table showDisclosureForRow:(int)row </code>

Returns YES if a row shows the disclosure arrow, NO if not.

<code>- (BOOL)table:(General/UITable *)table disclosureClickableForRow:(int)row </code>

If YES, table row will be selected if user clicks on disclosure arrow. If NO, selection won't change ("dead spot" on arrow). If not implemented, defaults to YES.

<code>- (BOOL)table:(General/UITable*)table canSelectRow:(int)row;</code>

Return value indicates whether the row can be selected. Defaults to YES.

<code>- (void)tableRowSelected:(General/NSNotification*)notification;</code>

A General/UITableSelectionNotification notification. The notification's object is the General/UITable. Called when a row is clicked, even if it was already selected.

<code>- (void)tableSelectionDidChange:(General/NSNotification*)notification;</code>

A General/UITableSelectionDidChangeNotification notification. The notification's object is the General/UITable.

----

**
Misc. delegate methods
**


*scrollerDidScroll:
*scroller:adjustSmoothScrollEnd:velocity:
*scroller:shouldAdjustSmoothScrollEndForVelocity:
*table:willSwipeToDeleteRow:
*tableWillLoadVisibleCells:
*table:heightForRow:
*table:setObjectValue:forTableColumn:row:
*table:willDisplayRowsInRange:
*table:canDeleteRow:
*table:confirmDeleteRow:
*table:canInsertAtRow:


----

(moved General/UISectionList sample code to a new General/UISectionList page..)
