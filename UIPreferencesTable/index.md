---
layout: page
title: UIPreferencesTable
---



Subclass of General/UITable.

**General/UIPreferencesTable delegate methods**

<code>- (int)numberOfGroupsInPreferencesTable:(General/UIPreferencesTable*)aTable;

<code>- (int)preferencesTable:(General/UIPreferencesTable*)aTable numberOfRowsInGroup:(int)group;

<code>- (General/UIPreferencesTableCell*)preferencesTable:(General/UIPreferencesTable*)aTable cellForGroup:(int)group;

<code>- (float)preferencesTable:(General/UIPreferencesTable*)aTable heightForRow:(int)row inGroup:(int)group withProposedHeight:(float)proposed;

<code>- (BOOL)preferencesTable:(General/UIPreferencesTable*)aTable isLabelGroup:(int)group;

<code>- (General/UIPreferencesTableCell*)preferencesTable:(General/UIPreferencesTable*)aTable cellForRow:(int)row inGroup:(int)group;
