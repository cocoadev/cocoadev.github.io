---
layout: page
title: ObjectAsCollection
---



Question: Is it necessary to maintain a distinction between singular objects and collections containing only 1 object?

Real world example: 
I hand you a mug. What do you have? 

*A mug*

Heres another mug. Now what do you have?

*2 mugs*

Ok, lets call it a collection of 2 mugs.

*Whatever, same thing*

Now give me a mug. What do you have? A mug or a collection of 1 mugs?

*Same thing again isn't it?*

**Is a single object the same as a group of one objects?**
If so, should NSObject be able to behave as a collection of a single object?

(Open ended question)

What do you make of this?
    
//
//  SingleObjectEnumerator.h
//
//  Created by MikeAmy on Sun Mar 14 2004.
//  Copyright (c) 2004 MikeAmy. All rights reserved.
//

#import <Foundation/Foundation.h>

@interface SingleObjectEnumerator : NSEnumerator { object; }
- nextObject;
- allObjects;
@end

// if this class implements methods that are only overridden by 
// some of the collection subclasses, that could cause confusion, 
// eg NSSet could suddenly be able to respond to indexing, etc. 
@interface NSObject (SingleObjectCollection)
- (NSEnumerator *) objectEnumerator;
- (NSEnumerator *) reverseObjectEnumerator;
- (BOOL) containsObject:o;
- (int) count;// can you have a collection without the notion of count?
- (void) getObjects:(id *)aBuffer;
- (void) makeObjectsPerformSelector:(SEL)aSelector;
- (void) makeObjectsPerformSelector:(SEL)aSelector withObject:o;
@end

----

Possible Implementation (ie Totally off the top of my head and untested![ie it don't work!])

//
//  SingleObjectEnumerator.m
//
//  Created by Michael Amy on Sun Mar 14 2004.
//  Copyright (c) 2004 Michael Amy. All rights reserved.
//

#import "SingleObjectEnumerator.h"

@implementation SingleObjectEnumerator : NSEnumerator
- initWith:o
{
    self = [super init];
    object = o;
    return self
}
- nextObject
{
    id temp = object;
    object = nil;
    return temp;
}
- allObjects
{
    return object ?
    [NSArray arrayWithObjects:[self nextObject],nil] :
    [NSArray array];
}
@end

@implementation NSObject (SingleObjectCollection)
- (NSEnumerator *) objectEnumerator
{
    return [[[SingleObjectEnumerator alloc] initWith:self] autorelease];
}

- (NSEnumerator *) reverseObjectEnumerator
{
    return [self objectEnumerator];
}

- (BOOL) containsObject:o
{
    return self == o:
}

- (int) count
{
    return 1;
}

- (NSArray *) allObjects
{
    return [NSArray arrayWithObjects:self,nil];
}

- (id) anyObject
{
    return self;
}

- (id) member:(id)anObject
{
    return [self isEqual:anObject] ? self : nil];
}

- (void) getObjects:(id *)aBuffer
{
    *aBuffer = self;
}

/*
- (unsigned) indexOfObject:(id)anObject
{
    return [self isEqual:anObject] ? 0 : NSNotFound;
}

- (unsigned) indexOfObjectIdenticalTo:(id)anObject
{
    return (self == anObject) ? 0 : NSNotFound;
}

- (id) lastObject
{
    return self;
}
*/

- (void) makeObjectsPerformSelector:(SEL)aSelector
{
    if (aSelector != NULL) [self performSelector:aSelector];
    else [NSException raise:@"NSInvalidArgumentException" format:@""];
}

- (void) makeObjectsPerformSelector:(SEL)aSelector withObject:o
{
    if (aSelector != NULL) [self performSelector:aSelector withObject:o];
    else [NSException raise:@"NSInvalidArgumentException" format:@""];
}
/*
- (id) objectAtIndex:(unsigned)index
{
    if (index) [NSException raise:@"NSRangeException" format:@""];
    else return self;
}*/

@end


----

Hi Michael,

It's kind of an interesting question, but do you have any reasons why this would be useful?  'Cause I definitely have situations in my code where this would cause problems - namely I branch behavior based on whether an object is 'a collection' which I take to mean responds to objectEnumerator.  And treating every object as a collection would definitely result in broken behavior in the places where I've used this.

----
Well, you could write methods that could accept collection or single object arguments, a bit like varargs, but less quirky. For example you could write a print function which takes only 1 argument, but it could be either a single object or a collection. So I guess you can make single objects available to stand in for collections, ie homogeneity. The implementation just treats everything as a collection. - Mike

----

No offense, but why would you need to branch on such things? would you not omit such branching if you could treat your objects homogenous?

That said, I think the mug analogy is flawed -- you speak with the person not the mug, thus the person is always the container for the mugs, and he might only hold one mug, but you still talk to the person and not the mug.

----
Well, maybe the analogy is misleading, what if there is only one person considering the mugs. I'd paraphrase you and say you believe collections and single objects should be kept separate. Am I right? - Mike

----
My mind is not really made up about that -- generally I write all my functions to receive a range, which could be the range containing a single object, so there is no overhead at the call-site, and the function works with single objects and containers. So the need rarely exists in my programs -- question is, would such a feature not be able to cause problems? e.g. would the container overshadow object methods or the other way around (or at least cause some confusion about to whom one is actually sending messages)?

----
If you were expecting a keyed collection you could get in trouble, cause there's no key with a single object. It'd have to be an object-key pair. How could this be done? Maybe single objects can only get as far as performing as really abstract collections. - Mike

----

This reminds me of that tasty morsel from set theory - the one about the set of all sets that are members of themselves...

Even the cupholder that only holds one mug is not the same entity as the mug, Grasshopper.

----

I've done work on a language wherein vectors are transparent. I am of the opinion that transparent HigherOrderMessaging is an excellent thing.

----
Do you have any examples? And does anyone have counter examples where things screw up? - Mike

----

What is a *transparent vector*?

A see-thru mosquito. *rimshot*

----

I suspect it is not what he implied, but I suppose a std::valarray (C++ STL) would qualify.

----
I don't know, since I've forgotten anything I ever knew about C++, but the idea was that vectors--arrays--were a transparent feature of the language unless specifically requested. That is, a single object could be considered to be a vector of length 1, and a vector of objects could be messaged just like a single object, and would send the message to each of its members just so. It's a bit like using LSTrampoline (now CXLTrampoline) and doing all your messaging through a category on NSObject which allows     anyObject do] blah] in addition to the more usual     [[anyArray do] blah].

The advantages of this might not be terribly obvious at first glance, but internalizing loops like this is both powerful and interesting, especially when you apply it to not only objects, but to all types, including the primitives--and there are provisions in the language (called RK/Arc--woulda been just Arc, but [[PaulGraham got to that first (: ) for conversions between primitive types (which are much like their C counterparts for efficiency reasons) and vectors of those primitive types.

The mechanism for vectors of type A as opposed to type B is based on two things; one, the fact that all non-primitive types share a common structure (and so can be stored identically), and two, something like class clusters, which allows different implementations behind a common interface--you can treat a vector of floats identically to a vector of objects.

RK/Arc is the language I want to use, although I'm somewhat biased having co-developed the concept. I've even made a few spikes at a compiler, but I'm working full-time now and therefore do not have much time to put towards non-web programming.

-- RobRix

----

I've seen tons of places in my code where I do something like [anObject selectObjects:[NSArray arrayWithObject:anotherObject]]; just to cut down on code duplication. This would nicely get rid of that. On a related note to the post above, how much would it hurt performance-wise to have primitive types be objects? I can imagine it wouldn't be useable as, say, an Objective-C replacement, but a lot of programs really don't need that kind of speed... --DavidSmith

----
The great thing about ObjC is it's orthogonality to C.  That is, the syntax for accessing members is SO different from calling methods that you rarely break in to the class, even if things were labelled public, or in subclasses.  It also allows you to use all of your sneaky C optimization methods and black magic inside a nice wrapper so all any user sees is [object message];  I think that having access to all the things you can do in C is a good thing, and that's why the C bits are left unchanged... primitive types are primitive types... if you want object wrappers that you can use how about making NSMutableValue or categories on NSValue classes like myNumber = [myNumber incrementedValue];  (I have used this for serial numbers that get stored in dictionaries many times, it's longer than myNumber++ but it's nicer than the headaches of C++ operator overloading). 

----
This is an aesthetic thing, but no less important for that. My reasoning is: there ought only be one sort of thing. That is, *assume* that primitive types are just like all other types, but in reality optimize them a little so they work faster without changing the appearance at all. RK/Arc never got written, so I don't know if this would be successful. But for primitives-as-objects, I use IoLanguage. In fact, I use IoLanguage anyway. (And I'm intending to attempt the optimize-e.g.-numbers-even-more things once I've digested more of the runtime. -- RobRix, who is loving this discussion

----
Not to start a flamewar, but could you tell me what the "headaches of C++ operator overloading" is?

----
Well, like which function gets called? the member oneFish.operator+(twoFish) or the function operator+(oneFish,twoFish) when you call oneFish+twoFish ?  also what does that do? what should it return?  The operator overloading system is supposed to allow you to integrate your types with C, in fact that's the good and bad of C++ in the same breath.  Things in C++ get extremely complex because the extensions to C were made with the thought of trying to keep the extensions LOOKING like C.  Because operators get overloaded (in two ways), because an object looks kinda like a struct sometimes and a primitive other times, because it's possible to do things that make it unclear without a reference which function is getting called on what type etc. C++ is ugly.  There are some core concepts that I really truly appreciate about C++ (generics, STL, multiple inheritance (I fear not the beast), namespaces) but on the other hand it's the orthogonality to C that makes ObjC great. It's because message sending syntax is DIFFERENT from function calling or operator syntax you can tell that you are sending a message to an object.  I'm sorry getting a little off track again... okay, C++ overloaded operators have two forms making it ambiguous about which gets called, and because operator syntax makes the type look like a primitive type it makes reading the code difficult becuase does "<<" mean bitwise shifting, or stream output, or performing a multi-method.  How do I pronounce that name to talk about it with somebody I'm working together with?  Also I used to remember but can't now (which is a problem if I can't remember language basics) how you differentiate between method signatures for prefix or postfix operator++/--.  One can spend two years studying C++ without creating confidence to understand a chunk of code, ObjC can be learnt in a day.

----
Look at Spirit (http://www.boost.org/libs/spirit/index.html ), to get an idea of what can be accomplished with operator overloading. Spirit is a parser generator written in C++, but looks nothing like C++. Heavy use of STL, generics and operator overloading has created what could be called a language looking like BNF within C++. Now this is nice, because it seems to work, but a friend explained to me that using Spirit would be next-to impossible because of all the strange compiler errors. STL, generics and operator overloading are, apparently, not the best friend of the compiler.

----

Watch out -- that count routine needs to return a zero in some cases.

