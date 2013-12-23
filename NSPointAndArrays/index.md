---
layout: page
title: NSPointAndArrays
---

How do you make an array of NSPoints? I'm trying to generate a path using some points stored in a NSMutableArray. Any help?

-- JohnDevor


----

My first try at this was sacrificed to the wiki gods so let's try this again.  NSValue is designed to handle precisely this problem. -- Bo

To insert:
    
[myArray addObject:[NSValue valueWithPoint:myPoint]];

And to retrieve:
    
myPoint = myArray objectAtIndex:i] pointValue];



----

Hey learn something new every day!!! --zootbobbalu

----

Sweet... 

-- [[JohnDevor

----

If you want to go the C route, I believe NSPointArray is defined as NSPoint *. All standard warnings apply. -- RobRix

----

You might try an NSSet.  I believe it is a useful container for other objects with faster sorting/comparison than an NSArray.

-- SashaGelbart

NSSet is a great class, but if you need to preserve ordering, then it's of less utility. -- RobRix

Or you could do something completely amusing (and a waste of time) and simply make each NSPoint a data item and place it in the array. Why? I dunno...was too early in the morning to make up something else.

