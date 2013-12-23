---
layout: page
title: GettingAroundTheSizeLimitationOfNSMutableArray
---

Hi. I'm trying to create a program that loads a huge file with thousands of objects in NSMutableArray form.  However, when I try to load the file and then load that data to NSTableView it obviously overloads the memory etc.  I was wondering how to do this since it seems to be possible with programs like EarthDesk and the cities.  Is it possible? If so how?

Thanks.

----

If NSMutableArray and NSTableView have a limit, it is probably in the hundreds of thousands or in the millions. If you aren't passing that, then you're probably doing something wrong, or misinterpreting symptoms. What do you mean by "overloads the memory"?

----

I seem to recall that INT_MAX is the limit in both cases.

----

What happens if you don't load the array into a table view? I'd say the table view was slowing you down, not the array. See if you can provide the data lazily from your data source, i.e. only return a few table view-fulls. 

----

Use shark to see where your slowdowns are happening.  It'll probably be in an unexpected place.   Thousands of objects aren't a problem with NSArray.  Remember NSArray takes Objective-C objects, which are each dynamically allocated, and each have 4 bytes overhead (and the reference count is actually kept outside of the object, so there's extra space being consumed there).  If you have millions of objects, the millions of calls to malloc() will ruin anybody's day, in which case you might want to use a different architecture (say C++ objects in a vector, using a custom allocator that suballocates out of a large block)

----

Note, If you have hundreds of thousands or millions of elements, it might be time to start looking into a database solution.

