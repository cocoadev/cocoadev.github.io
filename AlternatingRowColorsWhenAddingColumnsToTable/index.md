---
layout: page
title: AlternatingRowColorsWhenAddingColumnsToTable
---

I'm having problems with NSTableView and having alternating row colors when I put data in them... 
is it something to do with my dynamic column generation?

I set up a table view with alternating row colors in the nib file (or set it programmatically in awakeFromNib).

I removed the default set of columns built from the nib file and built a new array of table columns from an array of
dictionaries containing header cell titles and column identifiers. The array of table columns is an instance variable in my class.

The cells of the new columns are not drawing their backgrounds correctly. Do I need to go all the way down to subclassing the cells
to get this to work? My table columns have default, min, and max widths set programmatically when they are added to my
array of table columns, and are automatically resizing with the table view that resizes with the window. When I enlarge the
window, before the columns autoresize, I see the correctly-drawn row bg colors before the resizing columns cover them up.

So far I am just doing this once, in the awakeFromNib method but I want the user to be able to customize the column selection.
The default table from the nib file has four columns, and the set loaded from the property list consists of six columns. The latter all get drawn,
and the table data source behaves such that the column identifiers and header cell titles seem to be set up correctly from the p-list.

The drawing problem only appears when I add data to the table, one row at a time. The unfilled rows draw properly.

I am aware of the (old) Tony Arnold AlternatingRowColors subclass from the pre-Panther days.

----

The textfield cells need to be told NOT to draw their backgrounds, using setDrawsBackground:  If they do, then they draw a white background, creating that white rectangle effect.

One way to do this is to set up the data cell, like this:

    
NSTextFieldCell *myPrefsCell= [[NSTextFieldCell alloc] initTextCell: @""];
//... Modify myPrefsCell to your delight, for example:
[myPrefsCell setDrawsBackground: NO];
[theTableColumnInQuestion setDataCell: myPrefsCell];
[myPrefsCell release];


- FranciscoTolmasky

----

I think I had this same type of problem too, a while ago.  What fixed it for me was to edit the Data Cells when building the new Table Columns. For example:

    NSButtonCell *cell = [[NSButtonCell alloc] init];
[newColumn setDataCell:cell]; 
[cell release];

