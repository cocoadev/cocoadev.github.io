---
layout: page
title: ExtendingNSMutableArray
---

This is strange (to me) .. I'm extending the NSMutableArray class, which in a simple example only overrides the count method.  But I'm getting the following runtime exception:

<NSInvalidArgumentException> *** -count only defined for abstract class.  Define -[IndexableMutableArray count]!

    
IndexableMutableArray.h:
@interface IndexableMutableArray : NSMutableArray
{
}

@end

IndexableMutableArray.m:

@implementation IndexableMutableArray

- (unsigned)count {
    NSLog(@"IndexableMutableArray:count");
    return [super count];
}
@end


What's wrong here??  Anyone?  :)

----
See ClassClusters for the question and TextFormattingRules for how to make your code not look terrible.

----
Excellent. Sorry for noobing out. Thanks for the tips.

