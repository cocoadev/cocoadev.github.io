---
layout: page
title: AddingNSTableColumns
---

I'm dynamically adding columns to a table view, but I'm having tremendous problems. Every time I delete all columns and add one column, it adds two! Any idea?

    
//Delete all columns
NSArray * columnsIn = [mainView tableColumns];
for (i=0; i < [mainView numberOfColumns]; i++){
[mainView removeTableColumn:[columnsIn objectAtIndex:i]];
}

//Add a column
NSTableColumn * newColumn;
newColumn = [[NSTableColumn alloc] initWithIdentifier:@"eone"];
newColumn headerCell] setStringValue:[item valueForKey:@"oen";
[mainView addTableColumn:newColumn];


Again, this generates two which is really bugging me. Any help is appreciated, thanks.

----
    
//Delete all columns
NSArray * columnsIn = [mainView tableColumns];
const unsigned max = [columnsIn count];
for (i=0; i < max; ++i) {
	[mainView removeTableColumn:[columnsIn objectAtIndex:i]];
}
.
.
.



----
This doesn't work either...getting an out of bounds error. Tables seem to delete fine with the other code.

----
No matter what, the original code won't work properly. As the columns are deleted, -numberOfColumns  will return smaller numbers, so you never hit the end of the array.

Both versions will fail if -tableColumns returns a reference to the table's internal array, rather than returning a copy. This is probably why the second version is getting an out-of-bounds error. The simplest solution would be one like this:

    
while([mainView numberOfColumns])
	[mainView removeTableColumn:
		mainView tableColumns] objectAtIndex:0;



----

On a bit of a side note, you should release your newly-created NSTableColumn after you've added it to your NSTableView.  If you don't, it'll cause a memory leak when it's removed from the table or the table is deallocated.  -- Bo

----
Sorry about my knee jerk solution causing an out of bounds issue. I made the assumption that since      - tableColumns  is returning an NSArray and not a NSMutableArray (direct reference or not) that the contents would not change while removing columns. My bad for not testing before posting though, and I know ran into this in the past; Should have known better. Now, for some more untested code, I guess you could always      NSArray * columnsIn = [[[mainView tableColumns] copy] autorelease]  to avoid all of the method calls of the other solutions.

----
Thanks for all the help, while works fine. On a side note, I do release the tableColumn but forgot to include it in the code. Thanks!

