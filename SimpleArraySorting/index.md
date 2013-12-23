---
layout: page
title: SimpleArraySorting
---



I want to sort an array of NSString either alphabetically, or sorted by length...
Yet, the NSSortDescriptors are confusing me...
There's probably a single line of code that can solve my problem, but I can't figure...

    - (void)sortUsingSelector:(SEL)comparator
In this method, how do I refer to the first and second object to be compared ?

-- Trax

----

The selector will be sent to one of your objects, with the other object as the parameter. For example:
*(Rewritten for more clarity)*
    
@implementation FooHolder
- (void)compareTo:(FooHolder *)other {
   if([self foo] < [other foo])
      return NSOrderedAscending;
   else if([self foo] > [other foo])
      return NSOrderedDescending;
   else
      return NSOrderedSame;
}
@end


----
In the example above, where does the variable "foo" come from? *Check out the rewritten version.*

To answer the original question: foo is an instance variable in the class that -compareTo: is in.

----

This may help:
http://theocacao.com/document.page/87/

It's a simple category that allows you to create inline arrays of sort descriptors. You can then use those descriptors on an array by calling -sortedArrayUsingDescriptors:.  - ScottStevenson

