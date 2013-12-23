---
layout: page
title: NSEnumeratorsIndex
---

Suppose I have an NSEnumerator set to [someArray objectEnumerator] and I'm cruising on through the enumerator using  while (something = [myEnumerator nextObject]) and suddenly I realize I want the index of "something" inside "someArray".  Currently, I'm faced with two lame options: put a ++ counter in the while loop or send indexOfObject to "someArray".  Is there any way to get the enumerator to tell me where I am in the array?  Thanks!

*An enumerator doesn't always come from an NSArray, so it shouldn't know about array indices. If you need the counter, use a for loop instead of an enumerator.*

Also, does anybody know exactly what the memory management scheme is for NSEnumerators... I genuinly hope they do something better than autorelease themselves...

*From what I've read, [someArray objectEnumerator] returns an autoreleased enumerator, which follows the standard for returning new objects from a method. I don't think they could do anything else. It can't release itself when you're done enumerating through it, because it doesn't know when you're done. There's no guarantee you'll go to the end or that you won't try to call nextObject again after it returns nil.*

**It definitely uses autorelease, as a quick test calling     objectEnumerator without an AutoReleasePool will quickly demonstrate.**

Also, when it says I should modify the mutable array, while enumerating through it, does that mean I should add, remove, move objects in the array, or does it mean that I should change of the isntance members of any of the objects contained by the array?  Does the enumerator rely on hash values?

*Should not. You should be safe changing the contents of the objects in the array since it's safe to change them in the enumerator and they're still in the array at the time. But I wouldn't move, add, or delete objects in the array while enumerating through them.*

Additionally, I want to know if enumerator are faster than for loops using objectAtIndex

----
With the following test code, it looks like enumeration is a little more than 2 times faster!  Wow!  But, the unfair thing about that, is that the for loop keeps having to evaluate count... 

    
#import <Foundation/Foundation.h>

NSArray *lotsOfItems();
void forLoop(NSArray *array);
void enumerationLoop(NSArray *array);

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
    
    NSArray *array = lotsOfItems();
    
    NSLog(@"Start for");
    forLoop(array);
    NSLog(@"Start enumer");
    enumerationLoop(array);
    NSLog(@"Stop enumer");
    
    [pool release];
    return 0;
}

NSArray *lotsOfItems()
{
    NSMutableArray *array = [[[NSMutableArray alloc] init] autorelease];
    int i;
    for (i=0; i<1000000; i++)
	[array addObject:[NSNumber numberWithInt:i]];
    return array;
}

void forLoop(NSArray *array)
{
    unsigned i;
    id object;
    for (i=0; i<[array count]; i++)
	object = [array objectAtIndex:i];
}

void enumerationLoop(NSArray *array)
{
    NSEnumerator *enumerator = [array objectEnumerator];
    id object;
    
    while (object = [enumerator nextObject])
    {
	
    }
}


However, if the array count is evaluated every itteration as in the follwing case
    
void fasterForLoop(NSArray *array)
{
    unsigned i, bound = [array count];
    id object;
    for (i=0; i<bound; i++)
	object = [array objectAtIndex:i];
}

The enumerator is only about 27% faster... which is still very nice.  The faster for loop can really only be used if the array isn't being modified, which is precisely what NSEnumerators are for.  So if you aren't modifying the array, you stand to gain a 27% speed gain by using an NSEnumerator!  

Does anyone want to confirm the numbers here?  Maybe my tests could be improved?  And the real question:  what the heck is the NSEnumerator doing!?

*If NSArray uses a linked list internally, which it probably does, the enumerator is remembering its location in the list and thus having an O(1) increment time. OTOH,     objectAtIndex: must run along the whole list each time, an O(n) operation. If this is the case, you will find that for much larger arrays the for loop suffers very degraded performance. This is why enumerators are good!*

I suspected we were dealing with linked lists somewhere along the line... if we are, then, that 27% means crap... we need to actually find the factor.  However, I think I heard that if you use CFArray to do you index accessing, you get memory+offset access times... I wish Apple would be so kind as to tell us what their classes are doing so that we'd know how to work with them.  

*If it's a mutable class, assume it's optimized for mutation. That means, in the case of NSMutableArray, that it's a linked list, since otherwise inserts are O(n) and memory-churning. OTOH, for NSMutableDictionary, assume it's a hash table, and thus expect your size hint and hashing function to have a big effect on performance degrading.*

*Although, having run your performance test myself, it seems to suggest a O(1) search time, so the linked-list idea wouldn't wash.*

----

*Back again. A short experiment suggests that searches are O(1), while inserts get more expensive the fuller the array is. Sadly, this is still true after calling     initWithCapacity: correctly. But at least searches are good :)*

