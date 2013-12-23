---
layout: page
title: CCDMessageDistributer
---



CCDMessageDistributer is a TrampolineObject which started as a (premature?) refactoring of BSTrampoline by ThomasCastiglione.
You might well be better off with BSTrampoline or LSTrampoline instead of CCDMessageDistributer in terms of performance. MPWFoundation
also has a nice implementation, but I'm not sure about IMP caching.

CCDMessageDistributer implements HigherOrderMessaging (all, collect, select, reject, combine) for objects that 
responds to objectEnumerator, such as NSArray, NSSet, NSDictionary. objects that respond to keyEnumerator get treated as keyed collections too :).

If you want to use CCDMessageDistributer, use it like this:
    
// sends message to all elements in myArray
myArray all] myMessage:arg1 withObject:arg2];
 
// sends message to all elements in myDictionary, and return result collection.
[[myDictionary collect] myMessage:arg1 withObject:arg2]; 

 // sends message to all elements in mySet, keeps those that returned YES
[[mySet select] testMethod];

// concatenate (string) elements in mySet
[[mySet combine] stringByAppendingString:nil];

You can also do things like this:
    
id distributer = [myCollection collect];
id collection1 = [distributer myMessage:arg1 withObject:arg2];
id collection2 = [distributer anotherMessage:arg];


The discussion page is [[MessageDistribution. All thoughts or advice or criticism welcomed. 

This is distributed under GNU. Crediting me if you use it in your own work is appreciated. If you do extend this class, please 
put a brief link on this page. 

Also a plea:
It is in your interest to fight software patents, whether you are an individual or a company!! See the electronic frontier foundation website.
also http://petition.eurolinux.org/index_html?LANG=en

There may be errors since this changed to the CCD prefix. please mention them here.

Peace, Love and Truth,
MikeAmy

----

CCDMessageDistributer.h

    
/*  CCDMessageDistributer.h
  Copyright (C) 2004 Michael Amy

This program is free software; you can redistribute it and/or
modify it under the terms of the GNU General Public License
as published by the Free Software Foundation; either version 2
of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA  02111-1307, USA.
*/

#import "Foundation/Foundation.h"

@interface NSObject (HigherOrderMessaging)
- (id) all;
- (id) collect;
- (id) select;
- (id) reject;
- (id) combine;
- (id) reverseCombine;
@end

// mutableCopy doesn't work on a Class object, unfortunately
@interface NSObject (MutableAware)
- (Class) mutableClass:(BOOL)mutable;
@end

@interface CCDCollectionMediator : NSObject { @protected id element; NSEnumerator *enumerator; }
+ (id) mediator:aCollection;
- (id) initWith:aCollection;
- (id) nextElement;
- (void) add:result to:aCollection;
@end

@interface CCDKeyed : CCDCollectionMediator { @protected id key, collection; }
@end

@interface CCDForwarder : NSProxy { @protected id recipient; }
- (id) with:r;
@end

@interface CCDMessageDistributer : NSObject { @protected id collection, mediator, target; }
+ (id) with:collection;
- (id) initWith:collection;
/*
@method prepare:
@param invocation       The invocation to prepare for.
@discussion     
 Receiver checks the methodSignature of the invocation and initialises the collection mediator.
 Override to do any other preparation.
*/
- (void) prepare:invocation;
/*
@method perform:
@param invocation       The invocation to invoke.
@discussion    
 Receiver invokes the invocation. Override to do any other per-invocation tasks.
 */
- (void) perform:invocation;
/*
@method conclude:
@param invocation       The invocation to conclude.
@discussion     
 Receiver sets invocation return value to nil using [invocation setReturnValue:].
 This method allows results to be returned to the sender. 
 Subclasses override this method to set the invocation result value to something more
 useful and do any final tasks. 
*/
- (void) conclude:invocation;
/*
 @method requiredType:
 @discussion same but in english for a descriptive exception.
*/
- (void) check:(NSInvocation *)invocation;

@end

@interface CCDCollect : CCDMessageDistributer { @protected id resultCollection, value; }
@end

@interface CCDSelect : CCDCollect { @protected BOOL keep; }
- (BOOL) willKeepResult;
@end

@interface CCDReject : CCDSelect
@end

@interface CCDReverseCombine : CCDMessageDistributer { @protected id result; }
@end

@interface CCDCombine : CCDReverseCombine
@end


----

CCDMessageDistributer.m

    
/*  CCDMessageDistributer.m
  Copyright (C) 2004 Michael Amy

This program is free software; you can redistribute it and/or
modify it under the terms of the GNU General Public License
as published by the Free Software Foundation; either version 2
of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA  02111-1307, USA.
*/

#import "CCDMessageDistributer.h"
#import "objc/objc-class.h"

static id nothing = nil;
void inline replaces(id *old,id new)
{
    [new retain];
    [*old release];
    *old = new;
}

@implementation CCDCollectionMediator : NSObject
- (void) dealloc
{
    [enumerator release];
    [super dealloc];
}

+ (id) mediator:aCollection
{
    return [ ([aCollection respondsToSelector:@selector(keyEnumerator)] ?
                [[[CCDKeyed class] :
                self)
        alloc] initWith:aCollection] autorelease];
}

- (id) initWith:c
{
    self = [super init];
    replaces(&enumerator,[c objectEnumerator]);
    return self;
}

- (id) nextElement
{
    element = [enumerator nextObject];
    return element;
}

- (void) add:object
          to:aCollection
{
    [aCollection addObject:object];
}

- (id) element
{
    return element;
}

@end

@implementation CCDKeyed : CCDCollectionMediator
- (id) initWith:c
{
    self = [super init];
    collection = c;
    replaces(&enumerator,[c keyEnumerator]);
    return self;
}

- (id) nextElement
{
    element = (key = [enumerator nextObject]) ? [collection objectForKey:key] : nil;
    return element;
}

- (void) add:object to:aCollection
{
    [aCollection setObject:object forKey:key];
}
@end


@implementation CCDForwarder : NSProxy
- (id) with:r
{
    replaces(&recipient,r);
    return self;
}

- (void) dealloc
{
    [recipient release];
    [super dealloc];
}

- (void) forwardInvocation:invocation
{
    [recipient forwardInvocation:invocation];
}

- (NSMethodSignature *) methodSignatureForSelector:(SEL)aSelector
{
    return [recipient methodSignature];
}

@end

@implementation CCDMessageDistributer : NSObject
- (id) prototypeMethod { return nil; }

- (NSMethodSignature *) methodSignature
{
    return [self methodSignatureForSelector:@selector(prototypeMethod)];
}

- (void) dealloc
{
    [collection release];
    [mediator release];
    [super dealloc];
}

+ (id) with:aCollection
{
    return [[[CCDForwarder alloc] with:[self alloc] initWith:aCollection] autorelease] ] autorelease];
}

- (id) initWith:aCollection
{
    [[NSAssert([aCollection respondsToSelector:@selector(objectEnumerator)],
             @"Object must respond to selector objectEnumerator to use messaging");
    replaces(&collection,aCollection);
    replaces(&mediator,[CCDCollectionMediator mediator:collection]);
    return self;
}

- (void) check:(NSInvocation *)invocation
{
    const char *requiredType = 
        self methodSignatureForSelector:(@selector(prototypeMethod))] methodReturnType];
    NSAssert3((strcmp([[invocation methodSignature] methodReturnType],
                      requiredType) == 0),
              @"%s requires %s to return the type which encodes as %s.",
              [self class]->name,
              [invocation selector],
              requiredType);
}

- (void) prepare:invocation
{ }

- (void) perform:invocation
{
    [invocation invokeWithTarget:target];
}

- (void) conclude:invocation
{
    [invocation setReturnValue:&nothing];
}

- (void) forwardInvocation:invocation
{
    [self prepare:invocation];
    while (target = [mediator nextElement]) [self perform:invocation];
    [self conclude:invocation];
}
@end

@implementation [[CCDCollect : CCDMessageDistributer
- (void) dealloc
{
    [resultCollection release];
    [super dealloc];
}
- (void) prepare:invocation
{
    [self check:invocation];
    resultCollection=[[collection class] mutableClass:YES] alloc] init];
}

- (void) perform:invocation
{
    [invocation invokeWithTarget:target];
    [invocation getReturnValue:&value];
    [mediator add:value to:resultCollection];
}

- (void) conclude:invocation
{
    [invocation setReturnValue:&resultCollection];
}
@end

@implementation [[CCDSelect : CCDCollect
- (BOOL) prototypeMethod { return NO; }

- (void) perform:invocation
{
    [invocation invokeWithTarget:target];
    [invocation getReturnValue:&keep];
    if ([self willKeepResult]) [mediator add:[mediator element] to:resultCollection];
}

- (void) conclude:invocation
{
    [invocation initWithMethodSignature:[NSMethodSignature signatureWithObjCTypes:"@^v^c"]];
    [super conclude:invocation];
}

- (BOOL) willKeepResult
{
    return keep;
}
@end

@implementation CCDReject : CCDSelect
- (BOOL) willKeepResult
{
    return !keep;
}
@end

@implementation CCDReverseCombine : CCDMessageDistributer
- (id) prototypeMethod:(id)element { return nil; }

- (NSMethodSignature *) methodSignature
{
    return [self methodSignatureForSelector:@selector(prototypeMethod:)];
}
- (void) prepare:invocation
{
    [invocation getArgument:&result atIndex:2];
    if (result && (target = [mediator nextElement])) [self perform:invocation];
    else result = [mediator nextElement];
}
- (void) getReturnValueOf:invocation
{
    [invocation getReturnValue:&result];
    NSAssert2((result != nil),
              @"%s using %s returned a nil value. Are all the elements mutable?",
              [self class]->name,
              [invocation selector]);
}
- (void) perform:invocation
{
    [invocation setArgument:&result atIndex:2];
    [invocation invokeWithTarget:target];
    [self getReturnValueOf:invocation];
}
- (void) conclude:invocation
{
    [invocation setReturnValue:&result];
}
@end

@implementation CCDCombine : CCDReverseCombine
- (void) perform:invocation
{
    [invocation setArgument:&target atIndex:2];
    [invocation invokeWithTarget:result];
    [self getReturnValueOf:invocation];
}
@end

@implementation NSArray (MutableAware)
+ (Class) mutableClass:(BOOL)mutable
{
    return mutable ? [NSMutableArray class] : [NSArray class];
}
@end

@implementation NSDictionary (MutableAware)
+ (Class) mutableClass:(BOOL)mutable
{
    return mutable ? [NSMutableDictionary class] : [NSDictionary class];
}
@end

@implementation NSSet (MutableAware)
+ (Class) mutableClass:(BOOL)mutable
{
    return mutable ? [NSMutableSet class] : [NSSet class];
}
@end

@implementation NSObject (HigherOrderMessaging)
- (id) all
{
    return [CCDMessageDistributer with:self];
}

- (id) collect
{
    return [CCDCollect with:self];
}

- (id) select
{
    return [CCDSelect with:self];
}

- (id) reject
{
    return [CCDReject with:self];
}

- (id) combine
{
    return [CCDCombine with:self];
}

- (id) reverseCombine
{
    return [CCDReverseCombine with:self];
}

@end



----

You can use this for testing: 

    
#import <Foundation/Foundation.h>
#import "CCDMessageDistributer.h"

void logElementsOf(id collection, NSString *operation)
{
    NSLog(@"Testing %@;",operation);
    NSString *index;
    NSEnumerator *enumerator = [collection objectEnumerator];
    if (enumerator == nil)
    {
        NSLog(@"Enumerator is missing - did the invocation really return an object?");
    }
    while (index = [enumerator nextObject])
    {
        NSLog(index);
    }
}

void test(id collection)
{
    logElementsOf(collection,@"initial elements are");
    collection all] appendString:@"_"];
    logElementsOf(collection,@"all");
    logElementsOf([[collection collect] stringByAppendingString:@"_"],@"collect");
    logElementsOf([[collection select] hasPrefix:@"a"],@"select");
    logElementsOf([[collection reject] hasPrefix:@"a"],@"reject");
    [[NSLog(@"Testing combine; %@",
          collection combine] stringByAppendingString:nil]);
    [[NSLog(@"Testing combine into >; %@",
          collection combine] stringByAppendingString:[[[NSMutableString stringWithString:@">"]]);
    NSLog(@"Testing reverse combine; %@",
          collection reverseCombine] stringByAppendingString:nil]);
    [[NSLog(@"Testing reverse combine into >; %@",
          collection reverseCombine] stringByAppendingString:[[[NSMutableString stringWithString:@">"]]);
}

int main (int argc, const char * argv[])
{
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

    NSMutableString 
    *a = [NSMutableString stringWithString:@"a"], 
    *b = [NSMutableString stringWithString:@"b"], 
    *c = [NSMutableString stringWithString:@"c"];
    
    NSLog(@"------- NSArray -------");
    NSArray *array = [[NSArray alloc] initWithObjects:a, b, c, nil];
    test(array);
    
    NSLog(@"------- NSDictionary -------");
    NSDictionary *dict = [[NSDictionary alloc] initWithObjects:array forKeys:array];
    test(dict);
    
    NSLog(@"------- NSSet -------");
    NSSet *set = [[NSSet alloc] initWithObjects:a, b, c, nil];
    test(set);

    [array release];
    [pool release];
    return 0;
}



----

This produces:

    
------- NSArray -------
Testing: initial elements are
a
b
c
Testing: all
a_
b_
c_
Testing: collect
a__
b__
c__
Testing: select
a_
Testing: reject
b_
c_
Testing combine; a_b_c_
Testing combine into >; >a_b_c_
Testing reverse combine; c_b_a_
Testing reverse combine into >; c_b_a_>
------- NSDictionary -------
Testing: initial elements are
c_
a_
b_
Testing: all
c__
a__
b__
Testing: collect
c___
a___
b___
Testing: select
a__
Testing: reject
c__
b__
Testing combine; c__a__b__
Testing combine into >; >c__a__b__
Testing reverse combine; b__a__c__
Testing reverse combine into >; b__a__c__>
------- NSSet -------
Testing: initial elements are
c__
a__
b__
Testing: all
c___
a___
b___
Testing: collect
c____
a____
b____
Testing: select
a___
Testing: reject
c___
b___
Testing combine; c___a___b___
Testing combine into >; >c___a___b___
Testing reverse combine; b___a___c___
Testing reverse combine into >; b___a___c___>


If you need to know how it works, read on

**Classes and their roles**

Supporting classes are CCDForwarder and CCDCollectionMediator. 
Subclasses are CCDCollect, CCDSelect, CCDReject and CCDCombine|CCDReverseCombine. 

CCDMessageDistributer coordinates the process, sending messages to all elements in the collection.
CCDCollect collects the results, and returns them as the result of the invocation.
CCDSelect keeps elements which returned YES.
CCDReject keeps elements which returned NO.
CCDCombine and CCDReverseCombine combine results into a single value.
CCDCollectionMediator knows how to access elements of a collection. There is a subclass for keyed collections.
CCDForwarder decouples forwarding from messaging. This means CCDMessageDistributer inherits any NSObject categories.

It "should" be fairly trivial to write a subclass, for example you could change:

*The way the CCDMessageDistributer works. How about a category to take advantage of multiple processors?
*The kind of collection - could extend to trees etc., any other properties of the
collection. 
*The kind of result collection returned - could take a dictionary but return an array etc. 
Not sure if that is useful though.


Contrasting with BSTrampoline, "do" is now "all" because reusing keywords seems a bit messy and "all" 
seems right, semantically. If enough people complain I'll change it back. But I like "all".

**Extending**

To write a subclass, override any or all of the methods below: 

If you want to do something with the invocation, override:
*prepare:       - before any messages are sent.
*perform:       - sending of each method. You should setup (if necessary) and invoke the method.
*conclude:     - used to pass back the result collection in the invocation, or do any concluding tasks.
*prototypeMethod - an example of the type of method handled.
 *forwardInvocation: - to alter the way the CCDMessageDistributer works, eg for SMP

You should also add a category to NSObject with a method to create your messager with a collection.

**Notes on Combine**

(NB: combine|reverseCombine are comparable to foldl|foldr in Haskell )

CCDCombine combines the elements in turn, using the method specified, to produce a single result of the same type as the elements in the collection. CCDCombine therefore only accepts methods that take an object as argument and return another object which also responds to the same method.

For example, stringByAppendingString: which takes a string as target and argument, and returns a string.
Using combine and stringByAppendingString we can append all strings in a collection thusly:

   myStrings combine] stringByAppendingString:nil];

 nil is used because that argument will be replaced using elements from the collection. If an argument is specified, it will be used before the first element. This allows you to "carry on" a calculation over several collections. Make sure the argument can respond meaningfully to the selector - this may mean it needs to be mutable.
 For example:

   [[myStrings combine] stringByAppendingString:[[[NSMutableString stringWithString:@"> "]];

 joins all the strings in the collection onto "> "

 Any other arguments to the method are left untouched, and used every time.

 Take a really simple example - summing NSNumbers. We have a method called plus:, denoted "+". [number plus:anotherNumber] returns the sum of the two numbers as an NSNumber. Our collection contains 5 elements, each of which is an NSNumber:

 [@1,@2,@3,@4,@5]

 This is a simplified representation of what will happen:

    
 [@1 +:@2]         the 1st and 2nd NSNumbers are plus:ed and stored in the result, denoted @,
     |
    [@ +:@3]       the result is plus:ed with the 3rd NSNumber,
       |
      [@ +:@4]     that result is plus:ed with the 4th NSNumber,
         |
        [@ +:@5]   and so on,
           |
           v
         result    the final result is returned as the returnValue of the original invocation.


Combine uses the previous result as a target, with the next element as first argument. Reverse combine does things the opposite way round, with the result as argument and the next element as target, which may affect the final result depending on the method used. If your result comes out in reverse, use the opposite version.

----

We should put together some numbers comparing e.g. performance between LSTrampoline, BSTrampoline, and this.

The numbers would be mostly for my own benefit in deciding whether to continue using LSTrampoline (now CXLTrampoline) or to move to CCDMessageDistributor. (Probably I'll stick with the CXL one cos of the GPL, but I'm impressed with the whole combine/reverse-combine thing, and I know I'd not have thought to package specific HOM operations as objects in MediatorPattern.)

-- RobRix

Yeah I should have put up a comparison already. I do think that it was presumptious of me to use the CCD prefix for cocoadev. In fact thinking about it, that's more likely to cause a namespace conflict than anything else. And who decides what goes under CCD? So maybe I should move it yet again : ( and engage brain before clicking mouse next time.

We could do a quick, time-based comparison using the test routines, but I'm pretty sure CCDMessageDistributer will come off worse, because of the extra message unraveling involved, and probably come off slightly worse memory wise because of the extra objects created. But these are constant and O(1) complexity differences, so for me, I don't worry, I buy a better machine. The point about CCDMessageDistributer is it should be easy to extend with new HOM operations.  -- MikeAmy

*My expectation would be that the numbers would read slightly in favour of [BL]STrampoline, but like you, I don't worry; it's just a matter of 'fast enough'-- in (for instance) LodeStone, I use HOM (a lot) in the generating of bezier patches, so there are potentially thousands of message-sends to said patch when I change it. Right now, it's fast enough; as long as it's fast enough with *MessageDistributor too, there's no problem for me in changing.*

----

