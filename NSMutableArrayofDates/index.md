---
layout: page
title: NSMutableArrayofDates
---



I have in my NSArray, a bunch of date descriptions, stored as strings, that look like     2005-08-18 12:00:00 -0700.  Is there anyway, in an NSTableView, to get these to display with a formatter, such as Thursday, August 18, 2005?  I dynamically create my columns, and I tried adding this, but to no avail:

    NSCell *datecell = [[NSCell alloc] init];
[datecell setFormatter:[[NSDateFormatter alloc] initWithDateFormat:@"%A, %B %e, %Y" allowNaturalLanguage:NO]];
[newColumn setDataCell:datecell];

(i've tried it with both allowNaturalLanguage set to YES and NO, and neither worked)

----

Why are the dates stored as date descriptions? Why not store them as date objects?

----

This is just how I've stored them so far, and it would be easier if I could convert it rather than change my whole program to use NSDates.  I've tried just changing them, and the program threw a couple of errors that I couldn't solve immediately, so I would rather do it this way...

----

Write a value transformer that takes your strings and converts them to NSDate<nowiki/>s. Then your formatter will take the NSDate<nowiki/>s and format them as desired.

----

You know what?  I just had an epiphany, and I did kind of what the last person mentioned....

    - (void)tableView:(NSTableView *)aTableView
  willDisplayCell:(id)aCell
   forTableColumn:(NSTableColumn *)aTableColumn
			  row:(int)rowIndex
{
	if( aTableColumn identifier] isEqualToString:@"Date"] ) {
		
		[[NSDate *date = [NSDate dateWithNaturalLanguageString:objectDictionary objectAtIndex:rowIndex] objectForKey:@"Date";
		
		[aCell setStringValue:[date descriptionWithCalendarFormat:@"%A, %B %e, %Y" timeZone:nil locale:nil]];
	}
}

