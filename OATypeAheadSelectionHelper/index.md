---
layout: page
title: OATypeAheadSelectionHelper
---



This class provides a nice generic method for implementing type-ahead selection in any collection of selectable objects. An example is in the Finder, where you can start typing a file's name and it will be highlighted.

To use OATypeAheadSelectionHelper, you instantiate one, provide a data source (setDataSource:), and implement the following protocol in that data source:

    - (NSArray *)typeAheadSelectionItems;
This is where we build the list of possible items which the user can select by typing the first few letters. You should return an array of NSStrings.

    - (NSString *)currentlySelectedItem;
Type-ahead-selection behavior can change if an item is currently selected (especially if the item was selected by type-ahead-selection). Return nil if you have no selection or a multiple selection.

    - (void)typeAheadSelectItemAtIndex:(int)itemIndex;
We call this when a type-ahead-selection match has been made; you should select the item based on its index in the array you provided in -typeAheadSelectionItems.
    
----
Some text is copyright 2001-2003 Omni Development, Inc. See OmniSourceLicense.

