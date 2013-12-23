---
layout: page
title: SortingNSMutableArrayNewbieQuestion
---



I'm yet another newbie trying to use some simple test apps to get a better understanding of Cocoa..
I have an NSMutableArray of Transaction objects {Date, Name, Account, Amount}, which is manipulated with an NSArrayController/NSTableView in a Document based app.

I am trying to do some calculations in my MyDocument.m, and I'm a bit confused.  I have found several examples on sorting Cocoa objects like NSStrings, NSNumbers, etc.   But, I have those in my Transaction objects.      Do I need to create a 'compare' method for my class, or can I tell sort to use the NSDate/date element in my transaction object?

I believe I would use this:
[transactions sortUsingSelector:@selector(compare:)];

If I can tell it to use the 'date' in each object.

Thanks for any tips..
----
Unfortunately, Cocoa doesn't make this simple, but it's not that hard. Basically, you have to use NSSortDescriptor. --JediKnil
    
NSSortDescriptor *sortByDate = [[NSSortDescriptor alloc] initWithKey:@"date" ascending:NO]; // or YES
NSArray *descriptors = [[NSArray alloc] initWithObjects:sortByDate, nil];
[transactions sortUsingDescriptors:descriptors];
[descriptors release];
[sortByDate release];


----
Thanks for the reply, JediKnil!

