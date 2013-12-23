---
layout: page
title: NSTableOverlappingColumnClipHelper
---


Describe NSTableOverlappingColumnClipHelper here.

I dont know what the title means and this is my problem. 
I have just coded my first application, and im leaning as i go, and the final hurdle is to alow my program to be saved.

I have an NSTableView which uses an NSDictionary instance  (wrapped in another object) as its datasource.
I have just performed an NSKeyedArchiver on the NSDictionary, the replacing the  NSDictionary  with the object returned by NSKeyedUnarchiver.

Running the program it crashes. I test the instance of NSDictionary throuout the program and its OK until it reaches this point....

- (id) tableView:(NSTableView *) aTableView
objectValueForTableColumn:(NSTableColumn *) aTableColumn
			 row:(int) rowIndex
{ 

NSLog(@"dictTest %@",myDict);

//rest of code	
}

And here the output of the log is "NSTableOverlappingColumnClipHelper" ?
A quick search on google, and on here, reveals nothing about "NSTableOverlappingColumnClipHelper". Anybody come across this before?

What does it mean?

----

It might mean that you have a memory management problem.  Your object may have been deallocated, and the space it occupied reused for something random (in this case, an object of some private class used by the AppKit).

