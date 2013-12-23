---
layout: page
title: ClassClusters
---




ClassClusters are a concept wherein you have several classes that look and act to the developers using them like one. This is beneficial when one wants several implementations of the same interface that can be freely interchanged. NSString, NSNumber, and the FoundationCollections are excellent examples of transparent ClassClusters. 

See also: The description for AnObject goes a little bit into this concept.

http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaObjects/chapter_3_section_9.html#//apple_ref/doc/uid/TP40002974-CH4-SW34 (Apple's documentation)

----
**Creating your own ClassClusters**
* part1 - The MyMatrix allocators
* part2 - The other MyMatrix methods
* part3 - The MyPlaceholderMatrix initializers
* part4 - The MyMatrixImplementation methods
* part5 - The MyMutableMatrix class and its subclasses
* part6 - copy and mutableCopy
* part7 - Adding other subclasses

----

Let us say we want to create a new class cluster for storing matrices. Inspired by Apple's shining examples of collections, we want two interface classes, MyMatrix and MyMutableMatrix, the second derived from the first, and initially only one implementation of each, MyMatrixImplementation and MyMutableMatrixImplementation.

But we don't want the user to know about these last two; they should be created purely by the alloc/init/etc methods of the interface classes, just like Apple's own. There are now several issues to consider.


* **part1 - The MyMatrix allocators**

If you look closely at what Apple's alloc methods return, you will see they return an unlisted intermediate class, eg NSPlaceholderDictionary from NSDictionary. So we want our allocators to do the same; let us define a new class, MyPlaceholderMatrix, derived from MyMatrix, and return an alloc of this from MyMatrix:

    
@implementation MyMatrix
+ (id)alloc
{
    return [MyPlaceholderMatrix alloc];
}
@end


A moment's thought reveals this code causes an infinite loop. What we actually want is to do this when MyMatrix's alloc is called directly, but not when it is called from a subclass. Correct the code so: http://www.thefunnyquotessayings.com/cool-hilarious-funny-quotes-sayings/

    
@implementation MyMatrix
+ (id)alloc
{
    if ([self isEqual:[MyMatrix class]])
        return [MyPlaceholderMatrix alloc];
    else
        return [super alloc];
}
+ (id)allocWithZone:(NSZone *)zone
{
    if ([self isEqual:[MyMatrix class]])
        return [MyPlaceholderMatrix allocWithZone:zone];
    else
        return [super allocWithZone:zone];
}
@end


* **part2 - The other MyMatrix methods**

There may well be more of these than is strictly necessary, for the convenience of the user. The essential ones - *primitive methods*, as Apple's documentation puts it - must be defined in all usable subclasses, and should be implemented in the base class to return an error, as in:

    
- (NSEnumerator *)objectEnumerator
{
    NSAssert(NO, @"Must implement a complete subclass of MyMatrix!");
    return nil;
}


Any others - *derived methods* - should be implemented in terms of the primitive ones:

    
- (unsigned)count
{ return [self objectEnumerator] allObjects] count]; }


Remember that subclasses can override methods with more efficient ones; these methods are a convenience.

* **part3 - The [[MyPlaceholderMatrix initializers**

These will constitute the entire useful code of the placeholder class, and as such are the only primitive methods of MyMatrix we will override. They must replace the placeholder matrix with a more useful subclass.

    
- (id)init
{
    NSZone *temp = [self zone];  // Must not call methods after release
    [self release];              // Placeholder no longer needed
    return [[MyMatrixImplementation allocWithZone:temp] init];
}


Note we have ensured correct zoning. See NSZone for more information.

* **part4 - The MyMatrixImplementation methods**

As discussed above, what we need to implement here is just the primitive methods; we can also re-implement any other methods for efficiency's sake. http://goo.gl/OeSCu

    
- (id)objectAtLocation:(id)loc
{
    return [matrix objectForKey:[NSNumber numberWithInt:[hash
        hashForLocation:loc]]];
}


* **part5 - The MyMutableMatrix class and its subclasses**

These are very similar to construct as the MyMatrix ones, so I shan't reiterate the same thing again.

* **part6 - copy and mutableCopy**

In keeping with Apple traditions, we will make copy return an immutable object regardless of the object copied; equally, mutableCopy will return a mutable one. Thus, we need to add these methods to the base class, MyMatrix, and we need them to rely on primitive methods.

    
- (id)copyWithZone:(NSZone *)zone
{
    return [[MyMatrixImplementation allocWithZone:zone]
                initWithMatrix:self];
}

- (id)mutableCopyWithZone:(NSZone *)zone;
{
    return [[MyMutableMatrixImplementation allocWithZone:zone]
                initWithMatrix:self];
}


A common optimization to this arises from noticing that copies of immutable classes need not be separate objects; one can just retain the original and return that. Apple does just that in its clusters, so we shall add the following to any immutable subclasses we may code:

    
- (id)copyWithZone:(NSZone *)zone
{
    if (NSShouldRetainWithZone(self, zone))
        return [self retain];
    else
        return [super copyWithZone:zone];
}


This ensures correct zoning (see NSZone) whilst preventing needless extra copies being made.

* **part7 - Adding other subclasses**

The nice thing about having a placeholder class is that the object you get after initialization can depend on what initialization method you picked. NSNumber, for example, decides what class to return based on what type you choose to store in it. We could now add a new matrix implementation that reads its data from a database, add in the corresponding init methods, and the user would never need to know an extra class was now knocking about behind the MyMatrix interface. And you need not worry about the user calling the wrong init method for the wrong subclass - they should never get the chance.

Lovely.


-- Kritter (comments appreciated)

----

Although I haven't reviewed it thoroughly, it looks like a very nice tutorial to creating class clusters! For those who want to see another example, I suggest you take a look at the http://www.gnustep.org source, which I believe will use class clusters for foundation collections, strings and numbers as well.



-- DavidRemahl

----

Thanks Kritter for the tut, now my code is much neater (not that it is).

-- peacha

----

Instead of

    
- (NSEnumerator *)objectEnumerator
{
    NSAssert(@"Must implement a complete subclass of MyMatrix!", NO);
    return nil;
}


consider

    
- (NSEnumerator *)objectEnumerator
{
    [self doesNotRecognizeSelector: _cmd];
}


You'd probably also want to implement MyPlaceholderMatrix as a singleton.

Best,

jd

If the former is the way Apple does it, I submit to that wisdom.

However, I have never been too fond of the latter because I'm not clear on how to get it to interact properly with zoning. If there is only one object created, how do we remember which zone we are supposed to create the object in? And if we create one singleton MyPlaceholderMatrix per zone, how do we know if a zone has been destroyed (because we either need to deallocate the object, or remove any reference to it)?

-- KritTer

----

*Why is zoning important here? If the default zone is used for the singleton, then this should be up for the duration of the program, thus the singleton does no harm (i.e. preventing an entire zone from being freed). -- Anon1*

I think zoning is an issue here because if the placeholder isn't allocated in the destination zone, how do you determine what zone to create the actual object in when you return it in place of the placeholder. If you can answer this question, then I think the singleton is definitely the better approach. -- Anon2

It makes sense to have objects which are allocated and freed together part of the same zone, like a window with all its subviews or an array with all its objects -- the reason for this is


* locality, i.e. the objects will often be referenced "together" so there is a cache advantage by having them close to each other in memory,

* another reason is that when e.g. the window is disposed, so is all the subviews, and thus the *entire* zone is now free and can be returned to the system -- whereas had one random object been left, you'd have one dis-continuos zone which entire size would count as used memory,

* yet another advantage is if you e.g. need to parse a HTML file into a DOM tree (consisting entirely of ObjectiveC objects) you can ensure that all allocations that make up this tree are from the same zone, and simply dispose the zone, rather than each object in your tree (which is much slower)  -- granted you are not dependant on the destructors of each object to be invoked (I'm not really sure this is such a good idea with ObjectiveC, but atleast it's a common technique used in C++ where everything is transparent to the programmer).



But I really fail to see any of these reasons used with the singleton argument -- here you just have one random object for which there is no "locality of reference", will never be disposed (before program exists), so just allocate it from the default memory zone (which is also used by malloc()).

--Anon1, aka AllanOdgaard

The problem is multithreading. If I allocate a new object in a particular zone, we need to store which zone somewhere, and in a multithreaded app that information cannot be stored in the proposed singleton. The problem is that we might end up with our object on the wrong zone because somebody in a different thread came along and altered the singleton to create another object in said (wrong) zone. So either we create a new singleton per zone - impossible since we cannot reliably dispose of them, etc - or per thread, with the same flaw. Or we just bite the bullet and forget the singleton idea. Or ban zones. Or multithreading. (Hey, felicitous five, whaddya know.)

Apple appears to have scrapped zones for now, by the way, so actually the argument is semi-moot.

The ideal solution would be to realloc the memory you used creating the temporary object to fit the proper one. Cocoa doesn't give us a way to do that, however, since there is no way to tell NSObject where to get its memory from. Very occasionally, I miss C++...at least until I remember none of this works in C++ anyway, so no big deal.

-- KritTer

A moderately obvious solution is to ensure that your intermediate class has enough storage to be recast (isa-swizzled) to any subclass. The practicality of this obviously depends on the variance in implementation sizes of the subclasses and the flexibility you intend to allow yourself in creating new ones. -- JensAyton
JensAyton's solution in general is dangerous. Not just for the reasons he stated, but because you should never isa-swizzle, and certainly not for a reason like this. Also remember that the runtime internals are increasingly being made private, for good reasons.

A better solution is to keep an NSMapTable of placeholder objects, using the zone as a key. Because the zone may get cleaned up and placeholders should never be deallocated (being essentially 'singletons' in a broad sense), the placeholders should actually be allocated in the default zone, but they should remember the zone for which they were allocated, so they can create instances  from concrete subclasses in the correct zone. The zone could be stored either in an indexed ivar, accessible through object_getIndexIvars. No problem if it becomes invalid, because it's just a pointer, and if the zone ceases to exist the placeholder will never be used. Here's some minimal implementation of this idea.

    
@implementation AbstractObject
- (id)allocWithZone:(NSZone *)aZone
{
    static NSMapTable *placeholders = NULL;
    if ([AbstractObject class] != self)
        return [super allocWithZone:aZone];
    if (aZone == NULL)
        aZone = NSDefaultMallocZone();
    if (placeholders == NULL)
        placeholders = NSCreateMapTable(NSNonOwnedPointerMapKeyCallBacks, NSNonRetainedObjectMapValueCallBacks, 0);
    id placeholder = NSMapGet(placeholders, aZone);
    if (placeholder == nil) {
        placeholder = [PlaceholderObject allocWithZone:aZone];
        NSMapInsert(placeholders, aZone, placeholder);
    }
    return placeholder;
}
@end

@implementation PlaceholderObject
- (id)allocWithZone:(NSZone *)aZone
{
    id placeholder = NSAllocateObject(self, sizeof(NSZone *), NSDefaultMallocZone());
    NSZone **storage = object_getIndexedIvars(placeholder);
    storage[0] = aZone;
    return placeholder;
}
- (id)init
{
    NSZone *zone = *(NSZone **)object_getIndexedIvars(self);
    return [[ConcreteObject allocWithZone:zone] init];
}
- (id)retain { return self; }
- (id)autorelease { return self; }
- (void)release {}
+ (NSUInteger)retainCount { return NSUIntegerMax; }
@end


-- Christiaan

----

Here is a simpler implementation:

    
// Cluster.h
// Class cluster example implemenation

#import <Cocoa/Cocoa.h>

@interface AbstractObject : NSObject
{
    // I have no storage
}
// Return a named concrete subclass
- (id)initWithName:(NSString *)aName;
@end


    
// Cluster.m
// Class cluster example implemenation

#import "Cluster.h"

@interface PlaceHolderObject : AbstractObject
@end

@interface ConcreteObject : AbstractObject
{
    NSString *name;
}
@end


@implementation AbstractObject

// Return a placeholder for [AbstractObject alloc], otherwise delegate to super
+ (id)allocWithZone:(NSZone *)zone
{
    if ([AbstractObject self] == self)
        return [PlaceHolderObject allocWithZone:zone];    
    return [super allocWithZone:zone];
}

// Release the placeholder and create a concrete subclass instance
- (id)initWithName:(NSString *)aName
{
    self = [super init];
    [self release];    
    return [[ConcreteObject alloc] initWithName:aName];
}

@end


// I'm responsible for storage
@implementation ConcreteObject

- (id)initWithName:(NSString *)aName
{
    self = [super init];
    if (self != nil) {
        name = [aName retain];
    }
    return self;
}

- (void)dealloc
{
    [name release];
    [super dealloc];
}

@end


// I'm a singleton that create concrete subclasses
@implementation PlaceHolderObject

static PlaceHolderObject *sharedPlaceHolder = nil;

+ (void)initialize
{
    if ([PlaceHolderObject self] == self)
        sharedPlaceHolder = NSAllocateObject(self, 0, NSDefaultMallocZone());
}

+ (id)allocWithZone:(NSZone *)zone
{
    return sharedPlaceHolder;
}

// Return a concrete subclass for [[AbstractObject alloc] init]. This
// implementation return the placeholder itself, which may work for
// immutable objects.
- (id)init
{
    self = [super init];
    return self;
}

// Disable reference count changes

- (id)retain {return self;}
- (void)release {}
- (id)autorelease {return self;}
- (id)copyWithZone:(NSZone *)zone {return self;}

@end


-- NirSoffer


----

There is one improvement I would make to NirSoffer's sample. This has to do with what the documentation says about initializers in a class cluster. 
Subclasses of a class cluster should never rely on inherited initializers other than the designated initializer, which for a class cluster is always -init. 
Therefore, it is good custom for inherited initializers to be invalidated by raising an exception. This is also what happens for Foundation's class clusters on Leopard. 
To achieve this, the public abstract superclass should just implement -initWithName: by calling -doesNotRecognizeSelector: and returning nil. 
The real initialization, creating an instance of the concrete subclass, should be defined in the placeholder class instead.

Related to this, it is wrong to return a placeholder instance from init. PlaceholderObject is still an abstract class, -init should always return an
instance from a concrete class.

To summarize, here's the modified example with the correct zoning included.

    
// Cluster.h
// Class cluster example implemenation

#import <Cocoa/Cocoa.h>

@interface AbstractObject : NSObject
{
    // I have no storage
}
// Return a named concrete subclass
- (id)initWithName:(NSString *)aName;
// A primitive method
- (NSString *)name;
@end


    
// Cluster.m
// Class cluster example implemenation

#import "Cluster.h"

@interface PlaceHolderObject : AbstractObject
@end

@interface ConcreteObject : AbstractObject
{
    NSString *name;
}
@end


@implementation AbstractObject

// Return a placeholder for [AbstractObject alloc], otherwise delegate to super
+ (id)allocWithZone:(NSZone *)zone
{
    if ([AbstractObject self] == self)
        return [PlaceHolderObject allocWithZone:zone];    
    return [super allocWithZone:zone];
}

// Custom initializers should be implemented by the placeholder returning the appropriate concrete class.
// The docs say that concrete subclasses of the cluster are not supposed to inherit custom initializers, so the correct thing is to make this explicit
- (id)initWithName:(NSString *)aName
{
    [self doesNotRecognizeSelector:_cmd];    
    return nil;
}

// An example of a primitive method, should be implemented by concrete subclasses
- (NSString *)name
{
    [self doesNotRecognizeSelector:_cmd];    
    return nil;
}

@end


// I'm responsible for storage
@implementation ConcreteObject

- (id)initWithName:(NSString *)aName
{
    self = [super init];
    if (self != nil) {
        name = [aName retain];
    }
    return self;
}

- (void)dealloc
{
    [name release];
    [super dealloc];
}

// Concrete subclasses must implement primitive methods
- (NSString *)name
{
    return name;
}

@end


// I'm a singleton that create concrete subclasses in the correct zone
@implementation PlaceHolderObject

static NSMapTable *sharedPlaceHolders = NULL;

+ (void)initialize
{
    if ([PlaceHolderObject self] == self)
        sharedPlaceHolders = NSCreateMapTable(NSNonOwnedPointerMapKeyCallBacks, NSNonRetainedObjectMapValueCallBacks, 0);
}

+ (id)allocWithZone:(NSZone *)zone
{
    if (zone == NULL)
        zone = NSDefaultMallocZone();
    id placeHolder = NSMapGet(sharedPlaceHolders, zone);
    if (placeHolder == nil) {
        placeHolder = NSAllocateObject(self, sizeof(NSZone *), NSDefaultMallocZone());
        NSZone **storage = object_getIndexedIvars(placeHolder);
        storage[0] = zone;
        NSMapInsert(sharedPlaceHolders, zone, placeHolder);
    }
    return placeHolder;
}

// Return a concrete subclass for [[AbstractObject alloc] init].
- (id)init
{
    NSZone *zone = *(NSZone **)object_getIndexedIvars(self);
    return [[ConcreteObject allocWithZone:zone] init];
}

- (id)initWithName:(NSString *)aName
{
    NSZone *zone = *(NSZone **)object_getIndexedIvars(self);
    return [[ConcreteObject allocWithZone:zone] initWithName:aName];
}

// Disable reference count changes

- (id)retain {return self;}
- (void)release {}
- (id)autorelease {return self;}

@end


-- Christiaan

----
It looks like there was a a few typos in the above code block. I've changed `allWithZone:` to `allocWithZone:` and removed the id from where placeholder was being allocated (you were creating a new object within the scope of the if, so even after being alloc'd the the object returned was nil) -- Terinjokes

----

----
In the original post, is it necessary to override both alloc and allocWithZone: since alloc's default implementation calls allocWithZone:? I suppose overriding both would ensure that the code works correctly if a superclass had a custom alloc implementation that did not invoke allocWithZone:. How important is it to override both? -- Andrew H

----

----
The importance of class clusters cannot be overlooked by developers.  So many software titles utilize classes to provide a rich user experience.  As for Andrew's question, overriding both would not be necessary, but would serve as a fail-safe to help avoid errors in your code.  However, overriding both and being sure your software has no glitches in the code can be very important. -- Tim B

----

