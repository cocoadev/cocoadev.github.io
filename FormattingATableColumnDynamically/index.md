---
layout: page
title: FormattingATableColumnDynamically
---



I have a one-column table into which I read several doubles as strings. I would like to format these doubles as dollars and cents---dollar sign, comma separated triplets, etc.  I realize that I could drop a formatter on the table column header, but I would like to do the formatting cell by cell as the doubles are read in because the column will also contain other kind of quantities.
I use the delegate 
 - (void)tableView:(NSTableView *)tableView willDisplayCell:(id)aCell forTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex.
 
I checked that it was correctly hooked up by using it with some code which colors the background of successive cells alternately red or blue as the numbers are read in.  This works fine. But when I try to use a number formatter (NSFormatter) with the delegate following a related example in the Apple Documentation, I am unsuccessful (crashes).  Am I trying to do something impossible (or non-feasible)?

--Paul

----

Additional note: dropping a formatter on the table column header is also unsuccessful.  It will only function for items entered into the table followed by a return.  Strings entered on the fly remain unformatted.

--Paul

----

If it's crashing, yes you're definitely doing something wrong.

----

My apologies for being hopelessly obscure.  Let me try again in simplified form. I am working with an NSTableView. I have dropped a formatter on the relevant table column header. I use the command 
[[NSMutableArray alloc] initWithObjects:doub1String,doub2String,doub3String,nil], where for example doub1String = @"123456.789". doub1String then appears in the table as: 123456.789. I would like it to appear as $123,456.79.  Two obvious ways of doing this are: enter each number manually from the keyboard and hit return-- which works, or format the strings ahead of time.
	It would be convenient, certainly not crucial, if the formatting could occur "automatically," i.e., without having to use either of these methods.  Is this possible?

--Paul

----

Hmmm, I think the NSNumberFormater will format NSNumbers, not NSStrings containing something that could be parsed as an NSNumber.

----

You are absolutely right---like many things--obvious in retrospect.  Many thanks!

--Paul

