---
layout: page
title: MakingMutableClassImmutable
---

Is there any way to make a mutable class immutable? Is it even necessary? For example, in the following code:

    

-(NSArray *)doSomethingIDontCareWhatJustGiveMeBackAnArray
{
    NSMutableArray *example = [[NSMutableArray alloc] init];
    [example addObject:@"blah"];
    return [example autorelease];
}


is it OK to return example or should I do something to it to convert it to a non mutable NSArray?
----
In the example as you have written it, I don't see a big problem with it. Although, If the return value was a mutable array that my class was maintaining, i.e. a member variable, I personally would definitely convert it to an immutable array before returning it. To do this, [NSArray arrayWithArray:anArray] is one option, anArray copy] autorelease] will also return an immutable array from a mutable variant.

*It will? I thought [[anArray copy] autorelease] just returned an autoreleased still-mutable copy. Does copy always return an immutable copy? Do we need to use mutableCopy if we want our copies to be mutable? Is this documented anywhere? This is the crux of my original question. I don't suppose it really matters much, unless you're making 'live' changes to the returned object; I'm just trying to make my code as efficient and correct as possible.*

----
Duh. Answered my own question with a simple search of Apple's [[NSCopying docs:

*The copy returned is immutable if the consideration �immutable vs. mutable� applies to the receiving object; otherwise the exact nature of the copy is determined by the class.*

Don't know why I couldn't find anything on this before... I think I was looking at the class documentation for the copy method.

