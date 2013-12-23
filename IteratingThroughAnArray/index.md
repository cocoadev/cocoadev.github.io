---
layout: page
title: IteratingThroughAnArray
---

**Note:**
Generally, optimization is something to worry about after your code builds and runs correctly. It is easy enough to re-write any looping code in the fashion outlined below if it makes sense to do so. But until you prove that your iterators are gobbling up time and resources, you should use the nice high level concept of NSEnumerator and its -nextObject method. (Outlined even further below. :-) ) It makes the code easier to read, follow and debug until you reach the optimization stage of development.

----

To iterate through an NSArray, you could use an NSEnumerator on the array,  http://goo.gl/OeSCu
but iterating using objectAtIndex: is a bit more efficient than nextObject.

    
int arrayCount = [customerArray count];
NSAutoreleasePool *pool =  [[NSAutoreleasePool alloc] init];
for (i = 0; i < arrayCount; i++) {
    customerArray objectAtIndex:i] doSomething;
}
[pool release];


You could also create and destroy the pool every iteration in the loop, 
but if arrayCount is particularly large, or doSomething is particularly 
complex, you may want to create and release a pool every so many 
iterations inside the loop, just to keep the high-water mark of 
autoreleased objects down, while not slowing the loop on every iteration:

Performance Enhanced (tm) Version: ;-)

    
int arrayCount = [customerArray count];
NSAutoreleasePool *pool = nil;
for (i = 0; i < arrayCount; i++) {
    if (i % 1000 == 0) {
        [pool release]; // If pool is nil, this is a no-op
        pool = [[NSAutoreleasePool alloc] init];
    }
    customerArray objectAtIndex:i] doSomething];
}
[pool release];


(Based on a mailing list post by [[MattWatson)

----

Using NSEnumerator to iterate through (or enumerate) an array (or any collection for that matter--NSDictionary, NSSet, etc) has the advantage over the use of the for-loop of being more object-oriented, and thus more flexible.  Notice how the above code uses the NSArray specific method objectAtIndex.  Suppose you had code where you wanted to dynamically change which collection is being enumerated, then the above code would break if the new collection wasn't an NSArray.  So we use the following code that is general to all Cocoa collections (as well as custom collection objects which adhere to the appropriate interfaces):

In the following snippet of code we assume that the collection object exists already and is of type NSArray, NSSet, NSDictionary, or any of their subclasses.  The variable for the collections object we will call collection.

    

// assume variable collection exists

NSEnumerator *e = [collection objectEnumerator];
id collectionMemberObject;

while ( (collectionMemberObject = [e nextObject]) ) {
    // do something with collectionMemberObject
}



And there you have it!  A different way of doing what was done above with the for-loop.  If speed and performance are a concern, then this probably isn't the best thing for you.  However, if flexibility  is a concern, then this is probably a better choice.  For a more in depth discussion of Enumerators (also called Iterators) refer to *Design Patterns* by Erich Gamma, et al., specifically the **Iterator** design pattern found on page 257.

----

I thought I'd add an elegant way of iterating an array that I've seen used in the Omni frameworks. It iterates the array in reverse order, but if that's not important, the method is very simple and easy to read. Of course, add the appropriate autorelease pool wrapper as shown above if such memory management is necessary in your application.

    
int i = [customerArray count];
while ( i-- ) {
    customerArray objectAtIndex:i] doSomething;
}


BrockBrandenberg

*You can, of course, use [customerArray objectAtIndex:j - i] where j = [customerArray count]. This gets you forward iteration in the same nice form. -- RobRix*

----

Consider doing things the Smalltalk way, with an implementation of CodeBlocks, such as OCBlock.  Iteration code becomes this:

    
[customerArray do:@{ :each | [each doSomething]; }];


Well, once I make a preprocessor... as it stands, it'd look like this:

    
[customerArray do:ocblock(:each | [each doSomething];)]];


Other HigherOrderMessaging messages such as -collect:, -select:, -reject:, -detect:, and -inject:into: are also implemented.  The only performance hit is parsing the block, so consider creating the block before entering any loops or repeatedly called methods.

--JoeOsborn

*And how, pray, do you implement -do:?*

-do: is (clearly) a method accepting a block; it's implemented to call the block with each object in the collection. That :each there is the block's argument. -- RobRix

----

If you don't want the speed hit of parsing and don't mind the speed hit of proxies, you can use trampolines for HigherOrderMessaging. I have done this, and so has MarcelWeiher in MPWFoundation; it lacks the complete flexibility of blocks, but lets you instead do this:

    
collection do] anySelector];


Note that my implementation now handles -select and -reject properly. Even if it *does* uses [[UndocumentedGoodness...

-inject:into: hasn't proven itself useful enough to me to be implemented, but I'm sure it could be; as for -detect:, I don't know what that does :) -- RobRix

-detect: is quite nice, it returns the first object for which the block evaluates to true.  Its companion, detect:ifNone:, is even handier.  Pass it a second block, which executes if no match is found.  --JoeOsborn

No reason -detect couldn't be done with trampolines, and if you needed -detect:ifNone: (which is indeed cool), if loops aren't *that* bad. if(detectedObject == nil) ...  -- RobRix

----

I recently wrote a simple category on NSMenu which added the method -removeAllItems. In this method, I iterated through the items in the menu and removed each one from it as follows:

    
int i;

for(i = 0; i < [self numberOfItems]; i++)
{
    [self removeItemAtIndex:0];
}


Looks great, right? Should do fine. But in practice, I found that it wasn't always removing every item. It would leave the second when it started with three items. What was happening?

This is the issue of mutating arrays while iterating through them. In short, **don't do it**.

*Ay ay ay tell me about it... I once spend DAYS (a long time ago) trying to delete items from a list box in RealBasic. IIRC, my solution was to start removing items at the end of the array to avoid the indexes changing midloop. In Cocoa, NSMutableArray's removeObject* methods would be the way to go*

And if you have to, like here, cache -numberOfItems in an int before you start the iteration.

-- RobRix

*Cacheing numberOfItems seems overkill. Just do while([self numberOfItems]) { [self removeItemAtIndex:0; } instead.*

-- Zone-MR

----

How is OCBlock better/different than NSArray's standard makeObjectsPerformSelector methods? (besides looking stranger ;)

*Although I'm not the author of OCBlock, I have some familiarity with blocks in general. They give you the option of running a block of code for each element--that doesn't at all equate with having each object perform a method.*

*To illustrate, you could write a block which would, for each element, simultaneously iterate through another array, one which the first array has absolutely know knowledge of. There are (obvious) ways of doing this with simple methods, but they tend to amount to code which is less... nice. For one thing, you write the block inline, which makes it more obvious what's going on--they're not meant to replace methods, merely to complement them.*

*Do I use blocks? No. I use HigherOrderMessaging, myself. But I **want** blocks... as a language feature.*

----
I'd like to elaborate on the advantage http://www.ovcio.com/ [http://penowo.blogspot.com/2012/05/motor-matic-injeksi-irit-harga-murah.html motor matic injeksi irit harga murah] blocks by placing the code 'inline' where it is used instead of where it is called. It can be a very easy, clean way to keep the MVC roles seperated. A controller class, for example, can use a block describing very technical, non-business code to be executed on a collection of business (model) objects. The code for the model class in this case isn't polluted with technical  methods.

A category for the business(model) object defined in the controller can acheive the same result, but as mentioned above, if you're looking at the method declaration instead of it's invocation, it's not really obvious why you need such a method in the first place.

--TimHart
----
Can anyone provide help with the first example? I tried compiling it in my project, but I get the error "error: `for' loop initial declaration used outside C99 mode"
Any Help? 

This can be fixed by changing the C Language Dialect option in your project's build settings to C99 or GNU99

----

Does using autorelease pools within your loop for big loops actually speed it up, or just reduce the memory size??

----

As long as there is enough physical memory, the loop w/o the auto release pool is probably faster, but marginally...

I have a question: if speed is that important (i.e. selecting     objectAtIndex: over NSEnumerator etc., then why use NSArray in the first place? ;)

----
I have a question, too.  NSArrays are horrible speed-wise when compared with C-arrays, but comparing the two is like apples and oranges, since NSArrays are much more like LinkedLists functionality wise.  It has always been my understanding that -[NSEnumerator nextObject] is faster than -[NSArray objectAtIndex:].  The reason being objectAtIndex is NOT a memory offset calculation, as is something like myCArray[index].  NSArray doesn't do any kind of pointer arithmetic.  I would guess BUT DO NOT KNOW that an NSArray is a multiheaded linked list... with some hash tables also maybe for fast retrieval and containment checks.
----
NSArray is based on CFArray, and IIRC CFArray does not allow for any enumeration interface, so the NSEnumerator is most likely implemented by storing the index and using     objectAtIndex: together with this.

About the implementation, the source of CFArray is in Darwin, and it uses a DeQue when there are less than 64K entries.

--AllanOdgaard

----
i was looking at the .h of CFArray and see, even if it is not clear enough for me, references to binary search algorithm. And NSArray are based on CFArray i suppose. The BSearch function is: CFIndex CFArrayBSearchValues(CFArrayRef theArray, CFRange range, const void *value, CFComparatorFunction comparator, void *context);
Does it mean that performing a find() (i mean indexOfObject:) is faster in a *sorted* NSArray than in the same *non sorted* NSArray ? does the order of the sort (least to greatest of greatest to lowest) influence the performance ?

another question, i use a lot of sortedArrayUsingFunction:context:arrayHint: and i never measured any speed differences using the arrayHint data... does anybody know how it is used, how the array is "analysed" by the hint method ?
-- Aurelien [http://www.blognesian.com Best Buy] [http://www.blognesian.com Wal Mart] [http://www.blognesian.com home depot]

