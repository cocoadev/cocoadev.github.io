---
layout: page
title: MutableDeepCopyAndUserDefaults
---

I have an app which stores an array of dictionaries in the user defaults.

During load I make a mutable copy of this array and may change the contents of some of the dictionaries throught the program.

At termination time I store this array back to the user defaults and invoke synchronise, just to be sure -- but nothing is saved!!!

The reason seems to be, that mutableCopy only copies the array, not the items, so when I later change these, I change the ones which are also in user defaults, and when I store back the array, isEqual will return YES. And despite the documentation for synchronise, it does not write back the user defaults, if nothing have changed.

So one solution is to first remove the array from the user defaults and then store it, that works, but I am thinking that I am making a mistake by changing an array which is shared with the user defaults.

So should I make a mutable copy of each array member, during load?

Is mutableCopy on an array guarantied to return an array of only mutable objects? or does the 'mutable' only apply to the array itself (in my case the dictionaries are mutable, but that could just be a coincidence)?

Naturally it would be nice to be able to do this *copy what is in user defaults* lazy, but is there any neat way I can check weather or not a member of the array is the original (as returned by user defaults) or my copy (other than storing an extra element in the dictionary)?

----

The best way I've found to make a deep copy of something is to actually make an archive of it and immediately unarchive it into a different variable.  The only down-side is that the object to be copied must implement NSCoding... which really isn't that big a deal.  Here's the code to do so:

    
id foo = [[SomeObject alloc] init];
id copyOfFoo = [NSUnarchiver unarchiveObjectWithData: [NSArchiver archivedDataWithRootObject: foo]];


Like I said, the limitation on this is that you have to be able to isolate those objects you wish to copy from the rest of the object graph, and then, you have to have all the objects to be copied implement NSCoding.  Not that big a deal, really.  In any case, this should work just fine for the example you gave without any extra work on your part.

AndrewMiner

----

In some situations, I use HOM via a trampoline, e.g.     copy = array collect] copy];

*But this is not a deep copy!?!*

It's a deep copy, just not a deep deep copy.

*I think it is well established terminology to speak of shallow and deep copies, where the latter is recursive. The example above is not a deep copy pr. common definition of the term.*

It is if the array doesn't contain any subarrays. Nevertheless it is neither shallow nor deep.

----

:-)

Point well taken.  The technique I mentioned is something I do when whatever the object is doesn't support [[NSCopying.

AndrewMiner

----

This touches on something I've been feeling for a while. I like using NSArray and NSDictionary, and I often start using them for the whole datastructure of my app, up until I want to encapsulate logic in the data objects. Problem is that issues with mutable/immutable, along with confusion on when a copy is made, especially with KVC, starts cropping up, and the feeling is not so nice anymore.

Also I made a design that perhaps deserves comments... I have a document object (no NSDocument) that keeps track of its core plist, which contains metadata and by relative paths references its attached files. I heavily use KVC against this object. The data structure for the live object is that I read in the plist, do a deep copy, and keep that single root object reference, setting and getting values out of it when requested via KVC methods. Maybe I should have typed instance variables instead.

JoakimArfvidsson

----

We could extend Dictionary and Array with a "deep copy" category. Any takers?

----

A lot of us probably already did:)

Here is my not quite finished solution, for anyone to improve. I realize that I used C99 style internal functions. I think C99 (or even GNU99) is great. The new C standards improves your ability to structure your code for readability.

Actually, I'm not even 100 % sure this works as expected, as it's old code.

    
@implementation NSDictionary (DeepCopy)

- (id)deepMutableCopy
{
	id copy(id obj) {
		id temp = [obj mutableCopy];
		if ( [temp isKindOfClass:[NSArray class]] ) {
			for ( int i = 0 ; i < [temp count] ; i++ )
				[temp replaceObjectAtIndex:i withObject:copy([temp objectAtIndex:i])];
		} else if ( [temp isKindOfClass:[NSDictionary class]] ) {
			NSEnumerator *enumerator = [temp keyEnumerator];
			NSString *nextKey;
			while (nextKey = [enumerator nextObject])
				[temp setObject:copy([temp objectForKey:nextKey]) forKey:nextKey];
		} 
		return temp;
	}
	
	return (copy(self));
}

@end


@implementation NSArray (DeepCopy)

- (id)deepMutableCopy
{
	id copy(id obj) {
		id temp = [obj mutableCopy];
		if ( [temp isKindOfClass:[NSArray class]] ) {
			for ( int i = 0 ; i < [temp count] ; i++ )
				[temp replaceObjectAtIndex:i withObject:copy([temp objectAtIndex:i])];
		} else if ( [temp isKindOfClass:[NSDictionary class]] ) {
			NSEnumerator *enumerator = [temp keyEnumerator];
			NSString *nextKey;
			while (nextKey = [enumerator nextObject])
				[temp setObject:copy([temp objectForKey:nextKey]) forKey:nextKey];
		}
		return temp;
	}
	
	return (copy(self));
}

@end


----

From what I can tell from the documentation,     -mutableCopy may not exist on every object, particularly if an object doesn't have a mutable/immutable distinction. Wouldn't it be better to check for     -mutableCopyWithZone: and call it if it exists, but otherwise call     -copy? Also, your use of inner functions makes each method prettier, but together it makes them highly redundant. I would prefer a shared function or, better yet, a     -mutableDeepCopy method on NSObject that could recursively call itself.

One other minor nitpick, you forgot an implementation for NSSet. ;-)

*Also, you cannot extend this to include new classes without changing everything. It's a horrible example of abusing isKindOfClass - go to bed! And no supper!*

----

Ok, I couldn't help but write up a quickie implementation according to my ideas above. Here it is. Comments are welcome as always.
    
@interface NSObject (MutableDeepCopy)

- mutableDeepCopy;

@end


@implementation NSObject (MutableDeepCopy)

- mutableDeepCopy
{
    if([self respondsToSelector:@selector(mutableCopyWithZone:)])
        return [self mutableCopy];
    else if([self respondsToSelector:@selector(copyWithZone:)])
        return [self copy];
    else
        return [self retain];
}

@end

@implementation NSDictionary (MutableDeepCopy)

- mutableDeepCopy
{
    NSMutableDictionary *newDictionary = [[NSMutableDictionary alloc] init];
    NSEnumerator *enumerator = [self keyEnumerator];
    id key;
    while((key = [enumerator nextObject]))
    {
        id obj = self objectForKey:key] mutableDeepCopy];
        [newDictionary setObject:obj forKey:key];
        [obj release];
    }
    return newDictionary;
}

@end

@implementation [[NSArray (MutableDeepCopy)

- mutableDeepCopy
{
    NSMutableArray *newArray = [[NSMutableArray alloc] init];
    NSEnumerator *enumerator = [self objectEnumerator];
    id obj;
    while((obj = [enumerator nextObject]))
    {
        obj = [obj mutableDeepCopy];
        [newArray addObject:obj];
        [obj release];
    }
    return newArray;
}

@end

@implementation NSSet (MutableDeepCopy)

- mutableDeepCopy
{
    NSMutableSet *newSet = [[NSMutableSet alloc] init];
    NSEnumerator *enumerator = [self objectEnumerator];
    id obj;
    while((obj = [enumerator nextObject]))
    {
        obj = [obj mutableDeepCopy];
        [newSet addObject:obj];
        [obj release];
    }
    return newSet;
}

@end


----
I have used the above implementation and, while for the life of me I can't figure out why, it seems that -copy is always a copy of the pointers.  Examining two dictionaries in the debugger using this method shows that the objects have different pointers but their components are identical, STILL.  So it seems not to work.

Consolation - Here's today's $1000 freebie: I have a program which has three layers of threads.  When the bottom thread has ready data, it passes it up to the next thread and so on, via Distributed Objects.  Problem was, when a thread finishes with all its data, it committed suicide, and that data, though theoretically already passed via DO to the next level, was being destroyed and causing the program to crash when next referencing said data.  The exact message actually was "NSDistantObject invalid".  This is due ultimately to the fact that said data was in the form of a dictionary and they are shallow-copied, meaning that only the top level is actually copied and the other items are once again referenced as in the first dictionary.  Therefore, the hunt began for a way to get the actual information from the dictionary and duplicate it, fully.  The best answer was NSArchiver, as mentioned in this thread, but with one caveat:  As per the apple docs, NSDistantObject does not support the NSArchiving protocol so you *cannot* call [NSArchiver archivedDataWithRootObject:myDistantObject]; it will crash.  You must archive the object into data in the thread from which the object will be sent, and then unarchive it at its destination.

In short, if you need to pass arbitrary objects between threads, and not just strings, the best method (I think) is to use archiving, as so (you must already know how to use DO to communicate between threads):

    

In bottom thread:
...
      owner = [[NSConnection 
	rootProxyForConnectionWithRegisteredName:@"myApp thread results depository" 
		host:nil] retain]; //Assumes in your main thread you have registered this name for self
      [owner setProtocolForProxy:@protocol(myThreadResultsProtocol)]; 
       //Assumes you have made this protocol somewhere, which consists of newItem:
...
       NSMutableDictionary *myDictionary = [self createDictionaryFromObject:anObject];
       [owner newItem:[NSArchiver archivedDataWithRootObject:myDictionary]];
...

In main thread:
- (void)newItem:(id)sender
{
	NSLock *newItemLock = [[NSLock alloc] init];
		[newItemLock lock];	

	NSDictionary *itemRetainer = [NSUnarchiver unarchiveObjectWithData:sender];

       //Do what your heart desires with itemRetainer, no thanks to -copy

	[newItemLock unlock];
	return;
}




This took two days to track down.  Rejoice!  I can't believe that NSObject doesn't implement a hardcore deep copy  "duplicate this resolving all pointers" method.  It's also especially shameful that NSDictionary's method initWithDictionary:copyItems:, which claims to copy the VALUES of the dictionary as well as the keys, doesn't do so recursively and therefore is only useful if you know, for a fact, that your dictionary has no other container objects.

----
Question:  Is there any way to change the name of a page without killing all the references to it?  I think this page should just be called "DeepCopying", but I've seen it linked to elsewhere.

----
Answer: change all of the references! :-) For this page, there's only three.

----
----

----
    
- (id)mutableDeepCopy
{
//if id serializable
	CFTypeRef copy = CFPropertyListCreateDeepCopy(kCFAllocatorDefault, (CFTypeRef)self, kCFPropertyListMutableContainers);
...
	// if copy
		return [(id)copy autorelease];


bottom line it's enough easy to implement and add to the NSMutableCopying protocol, moreover, deepCopies are always memory inefficient 
no matter what e.g a deep copy, but  here this is the fastest one, caveats, for sets; you have to create a tmp CFArray, 
that's quite lame because a set if not counted, that's just an array with guarded methods against duplicates.


----
//
// @ aside 
// @ for your multi thread abomination api you created (consumer/producer concept might be something you don't like) BTW problem, which has nothing to do with this topic.
//

having a pool and a retain might help but mostly it's the reason for zones to exist, allocating in a child thread then freeing 
from another is always a dead end, reallocating in the middle might be the worst case scenario, as it said, threads are blind to each other
that's the reason of all these: condition, sem, wait for me, tell me e.g ipc apis mankind created, 
but in your case a simple call to the modifier @synchronized (nope that's not just a mutex unlock/lock scope) 
will do it as you don't care about the returned result (thread state is running did it finish), performSelector on a thread id until done might help too, using notification ordered pool too. 

To conclude your thread issue, you created it by breaking simple rules, your design is bad.

-- citrouilles shall overcome

