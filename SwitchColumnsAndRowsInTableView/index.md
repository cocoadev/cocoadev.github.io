---
layout: page
title: SwitchColumnsAndRowsInTableView
---

I would like to "rotate" a General/NSTableView, in order to have the columns playing the role of rows, and rows playing the role of columns.

ie : display data for a set of related records, with columns representing individual records and rows representing the attributes of those records.

Is there a simple way to do this ?

---- 

It seems to me that you would just have to return the necessary values to the tableView, same as if it was behaving normally. The tableView gives you the column and row it is requesting data for, so you should be able to use those to determine which data to return.

---- 

I am also looking for a way to do this :-). Any help would be greatly appreciated. Thanks!

- Davey

----

Well, first you'll likely need to build the table programmatically. In other words, you won' be able to accomplish much in Interface Builder. Second, bindings is probably out of the question (or at least harder than handling it all with your own code)... if someone knows better, please correct me. I'm going to go along the lines of using a table datasource (the 'old fashioned' way).

Second, you'll need to make your datasource able to add /remove columns based on the number of records. 

Third, you'll need to return the number of *FIELDS* in the number-of-rows-in-table-view data source method (since columns are now rows and vice-versa).

Fourth, you'll need to implement the datasource doing everything you'd normally do, just swapping the roles of the columns and rows. It really is that simple (there should be no magic, in other words). IE, instead of using the current row (in the 'get data for column, row method), you'd use the *index* of the *current column*, then use the row index to determine the field. Basically, a complete reversal of roles.

Before you do any of this, get VERY VERY familiar with table data sources and how to manually manipulate columns, etc.
