---
layout: page
title: ChangingSortData
---



I've got the following code, which, when a table column is clicked, will sort the table:

    NSArray *temp = [NSArray arrayWithArray:allAssignments];
[allAssignments removeObjectsInArray:temp];
		
NSSortDescriptor *nameDescriptor=[[[NSSortDescriptor alloc] initWithKey:[tableColumn identifier] ascending:YES selector:@selector(compare:)] autorelease];
		
NSArray *sortDescriptors=[NSArray arrayWithObjects:nameDescriptor, nil];
		
temp = [temp sortedArrayUsingDescriptors:sortDescriptors];
[allAssignments addObjectsFromArray:temp];

however, in my NSArray that has all my data, I have dates, but stored as strings, like "Wednesday, August 17, 2005".  when it uses the sort, it does a string compare, and sorts alphabetically.  now, is there any way to convert the strings into dates, and use this information for the compare: function?  thanks for the help.
----
Use NSDate<nowiki/>s to store the data internally, and attach an NSDateFormatter to your table column's cell. Not only is this better style, it is also easy to do in InterfaceBuilder. Just drag the NSDateFormatter icon (in the text section on 10.4) onto the table column and release. Also, all that allocating and autoreleasing might rack up...wouldn't it be easier just to have     allAssignments be an NSMutableArray to begin with? Then you can use     sortUsingDescriptors: instead of     sortedArrayUsingDescriptors:. It's cleaner, probably faster, and likely uses less memory. --JediKnil

----

Yeah, I thought I might get a response like that.  I'm not sure why I stored them as strings to begin with, but I'll have to change them and my program to NSDate.  Now, I'm saving the items as NSDates like so:      [newDictionary setObject:[NSDate dateWithNaturalLanguageString:[dateField stringValue]] forKey:@"Date"]; and then saving the Dictionary item to the UserPrefs.  The item is saving correctly, but my NSTableView isn't reading the Date back correctly, either with or without a formatter.  What is going on here?

