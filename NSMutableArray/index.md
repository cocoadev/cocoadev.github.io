---
layout: page
title: NSMutableArray
---

Subclass of NSArray with the difference that it can be modified. See also: FoundationCollections, DesignArray 

Methods: 

* addObject:

Adds the given object to the end of the array.

* insertObject:atIndex:

Inserts the given object at index, moving the object there previously, and all objects at higher indices, one along.

* removeObject:

Removes all instances of an object in the array

* removeObjectAtIndex:

Removes the object at the given index.

* replaceObjectAtIndex:withObject:

Replaces the object at index with the one supplied. Returns an error if the index is out of bounds.

* sortUsingSelector:

Sorts an array; see SortUsingSelector

* removeObjectIdenticalTo:

checks for object identity before removing an object; see NSMutableArrayRemoveObjectIdenticalTo for an example of when this is necessary



Note that the array itself won't send out KeyValueObserving notifications when these methods are called. If your class changes an array, your class needs to call **willChange:valuesAtIndexes:forKey:**, etc., itself.

----
A few examples:
    
 NSMutableArray *array;                                                           //simply defines a mutable Array
 
 array = NSMutableArray alloc] init];                                     //saves memory for the Array and initializes it.
 
 arrayCount = [array count];                                      //counts all objects of this array and puts it into the arrayCount variable
 
 [array addObject:[NSNumber numberWithInt:15;      // appends "15" to the (mutable) array
 
 arrayVariable = [array objectAtIndex:378];            //returns the 379th Object of the array and puts it into arrayVariable 

Enough for today, I hope all examples are correct. This is primarily meant for a newbie who has eventually the same problems like I had them a short time ago.  ;-)

-- MartinWeil

----

To iterate through an array you can use an enumerator. If you for example have an array called myArray filled with objects you can do this:

    
 NSEnumerator * enumerator = [myArray objectEnumerator];
 id element;
 
 while(element = [enumerator nextObject])
 {
   // Do your thing with the object.
   NSLog(element);
 }


--Gabriel Falkenberg

For an even better way to iterate through an array, you can use OCBlock(or other CodeBlocks) with -[NSArray do:], whose declaration is -(void)do:(OCBlock *)aBlock.  The code above becomes:
    
 [myArray do:ocblock(:each | [self doMyThingWith:each];)

Another excellent HigherOrderMessage like this is -collect:, which returns "the array consisting of the results of aBlock invoked with each member of the receiver as an argument.  

--JoeOsborn

----

You could also use - (void)makeObjectsPerformSelector:(SEL)aSelector or - (void)makeObjectsPerformSelector:(SEL)aSelector withObject:(id)anObject which are inherited from NSArray

----

Here's an easy way to sort a NSMutableArray filled with NSStrings:

    
 NSMutableArray *a = [NSMutableArray arrayWithObjects:@"Cat", @"Dog", @"Fish", @"Squirrel", @"Bear", @"Turtle", nil];
 [a sortUsingSelector:@selector(compare:)];


----
And another way to enumerate an array (faster) if the order doesn't matter:
    
 unsigned count = [array count];
 while (count--) {
     id object = [array objectAtIndex:count];
 }



----

If the order doesn't matter (at all), NSSet is the thing to use.

----

Does +[NSMutableArray array] return a MutableArray or a plain NSArray?

It returns a mutable array, and here is an easy way to test it:
    
 if (NSMutableArray array] isKindOfClass:[NSMutableArray class) {
     NSLog(@"It's a mutable array.");
 }


----

+[NSMutableArray array] does return a mutable array (as you can see if you just try to add an object to it), but isKindOfClass won't prove it, because NSMutableArray is a subclass of NSArray. Try the same test with NSArray if you don't believe it:
    
 > NSMutableArray array isKindOfClass:NSMutableArray class
 true
 
 > NSArray array isKindOfClass:NSMutableArray class
 true
 
 > NSArray array addObject:NSObject new autorelease
 
 NSInternalInconsistencyException: *** -[NSCFArray addObject:]: mutating method sent to immutable object
 
 > NSMutableArray array addObject:NSObject new autorelease
 
 > 


*This happens because of TollFreeBridging. Both NSArray and NSMutableArray are actually implemented using NSCFArray, so the isKindOfClass: trick won't work here. As far as I know, there is no reliable, documented way to see whether an array is mutable or not besides trying it and seeing whether it works. However, you shouldn't be writing code that needs to check anyway.*

----

*.. However, you shouldn't be writing code that needs to check anyway.*

People often say this, but I only had the light come on just the other day:  You really *must* conform to the published interface with regard to what is mutable and what is not, because otherwise you can get seriously unexpected results.  For example:  Often a method that is declared to return an NSString will actually return a mutable object.  This is a natural thing to do if the string must be built up from pieces, and it doesn't break the method's contract; the method still returns an NSString.  

So, you can't depend on anything in particular being immutable.  

----

...and importantly, if something returns a mutable object when an immutable object is specified in the interface, you'd better not change the object. You have no idea what other objects are depending on that value. -CS

----
You can test. isKindOfClass is not ok , because NSArray and NSMutableArray are related. But isMemberOfClass is a different story. You can use that one. 

----
Should I be using [NSMutableArray array], NSMutableArray alloc] init], or (in Objective-C++) [NSMutableArray new]?
I remember reading that most things like [NSString string] just call alloc and init. --[[ACoolie

----

It doesn't really matter.  [NSMutableArray array] is a nice shortcut, but you have to remember to retain it, so the question really is a matter of NSMutableArray array] retain] versus [[NSMutableArray alloc] init].  I usually use the former.  The real convenience comes in when you need to statically fill the array; you can do it all in one message.  [[NSMutableArray arrayWithObjects:...] retain] is faster than [[NSMutableArray alloc] init] followed by numerous [NSMutableArray addObject:(id)] calls.

----
However,     array/    retain expands to     alloc-    init-    autorelease-    retain, which is at least somewhat less efficient. This may seem like [[PrematureOptimization but since it's about the same amount of typing anyway you might as well set your habit to     alloc/    init.     new is actually not specific to Objective-C++ but isn't very Cocoa-y; nevertheless I often use it out of laziness for an empty, retained array. As for     arrayWithObjects:, well, there is also an     initWithObjects: that you again might as well use. --JediKnil

----

I tend to use     NSArray array] mutableCopy]. I don't think it really matters all that much...until it does.

----

Doing that, at least conceptually, requires the creation of an empty [[NSArray object followed by the creation of a mutable copy--two objects instead of one. Apple might be optimizing it behind the scenes, but *conceptually* it's a waste of cycles and memory.

----
It's 27 characters (not counting whitespace), like alloc/init, so it doesn't save any typing either. But this way I create a needless and mindless chore that I can save until it's time to "look busy" while I think on other things.

----
If you really want to save on typing,     #define M NSMutableArray alloc] init] <grin>

----
I feel I must use this now that you suggested that;     #define MootableArray [[NSMutableArray alloc] init]

----
What of garbage collection and nested mutable arrays? Cocoa supports the automatic collection of [[NSMutableArray objects instantiated of the form [NSMutableArray array]. Once retained, NSMutableArray array] retain] they must be released. But, what happens to any nested [[NSMutableArray objects?

----
You seem to be confusing GarbageCollection with Cocoa's retain/release system, which is totally unrelated. In any case, see MemoryManagement. In short, you can count on the framework to do the right thing and manage memory for sub-objects.

----
If I have a method that creates a instance variable (an NSArray). But, in order to properly fill that NSArray, an NSMutableArray is used. I don't need/want the NSMutableArray methods of this variable anywhere else in the class. If memory is important, should I convert the NSMutableArray into an NSArray by:
    
 myInstanceVariable = NSArray arrayWithArray:otherArray] retain];
 [otherArray release];
Or does simply assigning the [[NSArray instance variable to an NSMutableArray release the extra memory (doubtful)?
Also, the situation is the same if I want to return an NSArray from a method that creates the NSArray with an NSMutableArray. -- ACoolie

----
OptimizeLater. Do what's easiest and makes the most sense.

----
Does anyone know if NSMutableArray is implemented with a dynamic array (called "vector" in C++ and "ArrayList" in Java) or a linked list (called "list" in C++ and "LinkedList" in Java)? These data structures have fundamentally opposite tradeoffs. Dynamic arrays store elements contiguously, and can index elements in constant time, and can add elements to the end in amortized constant time, but is is really inefficient to add or remove an element in the middle, because you have to shift all the following elements. Whereas for linked lists, you can add or remove an element in the middle in constant time as you traverse the list over the element, but it is really inefficient to index it. So I would like to know how it is implemented to see if it is right for my application. Although seeing as to how NSEnumerator doesn't have any mechanism for adding or removing elements while iterating (like ListIterator in Java), it wouldn't be able to take advantage of linked list's benefits anyway.

I am really disappointed that Cocoa does not provide separate implementations of collections data structures. No matter which implementation NSMutableArray uses, it's gonna slow somebody down. Of course, I could write my own subclasses, but that would defeat the point of the framework.

----
There is no one way NSMutableArray is implemented. It's a class cluster, which means the implementation depends on exactly what concrete subclass you get.

The most common concrete subclass, NSCFArray, does have performance guarantees. From the CFArray.h header:

    
 	The access time for a value in the array is guaranteed to be at
 	worst O(lg N) for any implementation, current and future, but will
 	often be O(1) (constant time). Linear search operations similarly
 	have a worst case complexity of O(N*lg N), though typically the
 	bounds will be tighter, and so on. Insertion or deletion operations
 	will typically be linear in the number of values in the array, but
 	may be O(N*lg N) clearly in the worst case in some implementations.
 	There are no favored positions within the array for performance;
 	that is, it is not necessarily faster to access values with low
 	indices, or to insert or delete values with high indices, or
 	whatever.


This suggests that it uses a hybrid approach.

----
I'm no expert, but with the time complexities at what they are, it really seems like a self-balancing binary search tree is being used in the NSCFArray.  The time complexity for access time is guaranteed to be O(lg N) if it is balanced perfectly.  Similarly the worst case time complexity for linear search is O(N*lg N).  I would, however, think the time complexity of insertion or deletion to be O(lg N) * time complexity of the balance operation.

----
problem...

groupArray is (NSMutableArray *)

When I use
    
 self.groupArray alloc] init];


I get a warning that [[NSMutableArray may not respond to init

    
 [self.groupArray initWithCapacity:6];


seems to be OK but still addObject does not seem to work...

    
 newGroup = [[HomeControlGroup alloc] initGroup:@"Hallway" groupNumber:0];
 [self.groupArray addObject: newGroup];


the newGroup is created properly - I can see it in the debugger but it does not get added.
Am I missing something really obvious?

----
ok - yes I am an idiot :(

    
 self.groupArray = [[NSMutableArray alloc] init];


works like a charm :)
----

