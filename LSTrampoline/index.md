---
layout: page
title: LSTrampoline
---



LSTrampoline is an implementation of a TrampolineObject for the purpose of HigherOrderMessaging. Enjoy!

----

LSTrampoline.h

I provide a protocol for convenience (though I currently make only haphazard use of it) for HOM. Other than that, this is pretty standard stuff; you init the trampoline with the target object (be it a collection or otherwise), the callback selector, and the signature that the trampoline should use. http://goo.gl/OeSCu

    
#import <Foundation/Foundation.h>

@protocol LSHOM
// add any messages you want all HOM objects to support
-(id)do;
-(id)collect;
-(id)select;
-(id)reject;

@end

@protocol LSHOMMath <LSHOM>

-(id)sumInt;
-(id)sumFloat;

@end

@interface LSTrampoline : NSProxy {
    id target;
    SEL selector;
    NSMethodSignature *signature;
    id idReturn;
    float floatReturn;
    int intReturn;
}

// init
-(id)initWithTarget:(id)t selector:(SEL)s signature:(NSMethodSignature *)sig;

// factory
+(id)trampolineWithTarget:(id)t selector:(SEL)s prototypeSelector:(SEL)p;

// access
-(void)setTarget:(id)t;
-(id<LSHOM>)target;

-(id)idReturn;
-(float)floatReturn;
-(int)intReturn;

@end


----

LSTrampoline.m

I make use of -initWithMethodSignature: and +signatureWithObjCTypes: just like in BSTrampoline. And I really ought to be using @encode() rather than the explicit "@^v^c" string. Eventually. This makes is crash on OS X/Intel.

    
#import "LSTrampoline.h"

@implementation LSTrampoline

// init
-(id)initWithTarget:(id)t selector:(SEL)s signature:(NSMethodSignature *)sig
{
    target = [t retain];
    selector = s;
    signature = [sig retain];
    return self;
}

-(id)init
{
    return [self initWithTarget:nil selector:@selector(class) signature:nil];
}


-(void)dealloc
{
    [target release];
    [signature release];
}


// factory
+(id)trampolineWithTarget:(id)t selector:(SEL)s prototypeSelector:(SEL)p
{
    return [self alloc] initWithTarget:t
        selector:s
        signature:[t methodSignatureForSelector:p
    autorelease];
}


// access
-(void)setTarget:(id)t
{
    id oldTarget = target;
    target = [t retain];
    [oldTarget release];
}

-(id)target
{
    return target;
}


-(void)forwardInvocation:(NSInvocation *)invocation
{
    const char *returnType =
        target methodSignatureForSelector:selector] methodReturnType];
    if(!strcmp(returnType, @encode(float)))
    {
        [target performSelector:selector withObject:invocation];
        [invocation getReturnValue:&floatReturn];
        [invocation initWithMethodSignature:
            [[[NSMethodSignature signatureWithObjCTypes:"f^v^c"]];
        [invocation setSelector:@selector(floatReturn)];
        [invocation invokeWithTarget:self];
        [invocation setReturnValue:&floatReturn];
	}
    else if(!strcmp(returnType, @encode(int)))
    {
        [target performSelector:selector withObject:invocation];
        [invocation getReturnValue:&intReturn];
        [invocation initWithMethodSignature:
            [NSMethodSignature signatureWithObjCTypes:"i^v^c"]];
        [invocation setSelector:@selector(intReturn)];
        [invocation invokeWithTarget:self];
        [invocation setReturnValue:&intReturn];
    }
    else
    {
        idReturn = [target performSelector:selector withObject:invocation];
        [invocation initWithMethodSignature:
            [NSMethodSignature signatureWithObjCTypes:"@^v^c"]];
        [invocation setSelector:@selector(idReturn)];
        [invocation invokeWithTarget:self];
        [invocation setReturnValue:&idReturn];
    }
}


-(NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector
{
    return signature;
}


-(id)idReturn
{
    return idReturn;
}

-(float)floatReturn
{
    return floatReturn;
}

-(int)intReturn
{
    return intReturn;
}

@end


----

Here is a category on NSArray for purposes of demonstration. This category's interface is just conforming to LSHOM and LSHOMMath declared in LSTrampoline.h above.

    
#import "LSTrampoline.h"

@implementation NSArray (HOM)

-(id)collect
{
    return [LSTrampoline trampolineWithTarget:self
        selector:@selector(collect:)
        prototypeSelector:@selector(collectPrototype)];
}

-(id)select
{
    return [LSTrampoline trampolineWithTarget:self
        selector:@selector(select:)
        prototypeSelector:@selector(selectPrototype)];
}

-(id)reject
{
    return [LSTrampoline trampolineWithTarget:self
        selector:@selector(reject:)
        prototypeSelector:@selector(selectPrototype)];
}

-(id)detect
{
    return [LSTrampoline trampolineWithTarget:self
        selector:@selector(detect:)
    prototypeSelector:@selector(collectPrototype)];
}

-(id)do
{
    return [LSTrampoline trampolineWithTarget:self
        selector:@selector(do:)
        prototypeSelector:@selector(doPrototype)];
}


-(id)sumInt
{
    return [LSTrampoline trampolineWithTarget:self
        selector:@selector(sumInt:)
        prototypeSelector:@selector(sumIntPrototype)];
}

-(id)sumFloat
{
    return [LSTrampoline trampolineWithTarget:self
        selector:@selector(sumFloat:)
        prototypeSelector:@selector(sumFloatPrototype)];
}


-(id)collectPrototype
{
    return nil;
}

-(BOOL)selectPrototype
{
    return NO;
}

-(void)doPrototype
{
    return;
}

-(int)sumIntPrototype
{
    return 0;
}

-(float)sumFloatPrototype
{
    return 0.0;
}


-(id)do:(NSInvocation *)invocation
{
    NSEnumerator *e = [self objectEnumerator];
    id temp;
    
    while(temp = [e nextObject])
    {
        [invocation invokeWithTarget:temp];
    }
    return nil;
}

-(id)collect:(NSInvocation *)invocation
{
    NSMutableArray *a = [NSMutableArray array];
    NSEnumerator *e = [self objectEnumerator];
    id temp;
    
    while(temp = [e nextObject])
    {
        id val;
        [invocation invokeWithTarget:temp];
        [invocation getReturnValue:&val];
        [a addObject:val];
    }
    return a;
}

-(id)select:(NSInvocation *)invocation
{
    NSMutableArray *a = [NSMutableArray array];
    NSEnumerator *e = [self objectEnumerator];
    id temp;
    
    while(temp = [e nextObject])
    {
        BOOL val;
        [invocation invokeWithTarget:temp];
        [invocation getReturnValue:&val];
        
        if(val)
            [a addObject:temp];
    }
    return a;
}

-(id)reject:(NSInvocation *)invocation
{
    NSMutableArray *a = [NSMutableArray array];
    NSEnumerator *e = [self objectEnumerator];
    id temp;
    
    while(temp = [e nextObject])
    {
        BOOL val;
        [invocation invokeWithTarget:temp];
        [invocation getReturnValue:&val];
        
        if(!val)
            [a addObject:temp];
    }
    return a;
}

-(id)detect:(NSInvocation *)invocation
{
    NSEnumerator *e = [self objectEnumerator];
    id temp, result = nil;
    
    while(temp = [e nextObject])
    {
        BOOL val;
        [invocation invokeWithTarget:temp];
        [invocation getReturnValue:&val];
        if(val)
            result = temp;
    }
    return result;
}


-(int)sumInt:(NSInvocation *)invocation
{
    NSEnumerator *e = [self objectEnumerator];
    id temp;
    int result = 0;
    
    while(temp = [e nextObject])
    {
        int val;
        [invocation invokeWithTarget:temp];
        [invocation getReturnValue:&val];
        result += val;	
    }
    [invocation setReturnValue:&result];
    return result;
}

-(float)sumFloat:(NSInvocation *)invocation
{
    NSEnumerator *e = [self objectEnumerator];
    id temp;
    float result = 0.0;
    
    while(temp = [e nextObject])
    {
        float val;
        [invocation invokeWithTarget:temp];
        [invocation getReturnValue:&val];
        result = result + val;
    }
    [invocation setReturnValue:&result];
    return result;
}

@end


Many thanks to ThomasCastiglione for BSTrampoline as an example of how to get LSTrampoline to stop crashing LodeStone. Thanks also to JoeOsborn for trying to get me to use blocks (must! not! rely! on! other! people's! code! Oh no, I have to implement Cocoa now! :D ), to KritTer for much discussion of HOM in general, and to MarcelWeiher for MPWFoundation and the spark for the interest. And as of March 15th, thanks to ChrisTh for help moving away from +signatureWithObjCTypes:! Getting closer to using only documented methods.

Suggestions, fixes, and the like are welcome; please make note of them on this page so there's a clear indication of what's changed!

Finally, LSHOMTest is a function which tests -do, -collect, -select, and -reject.

-- RobRix

See also BSTrampoline for an alternative implementation.

Note from the author of BSTrampoline: I wouldn't recommend it as an alternative, actually, LSTrampoline is a rather more general version useful for things other than just HOM on collections.

Just depends on your priorities, I suppose; BSTrampoline is pretty fast to get up and running. -- RobRix

----
**Changelog**
----


* Feb. 25th, 2003 - RobRix realized that -do in the category above should really be returning "v^v^c" instead of "@^v^c" This didn't affect LSHOMTest, but it was crashing LodeStone when I used -do on an NSSet. I also changed the implementations of -do: and -collect: to use NSEnumerator cos that way it's one-to-one between the implementation for NSArray and NSSet.

* Feb. 26th, 2003 - RobRix decided to note that one-to-one between NSArray HOM and NSSet HOM isn't likely, but for a different reason--NSSet HOM methods should be implemented with NSMutableSet rather than NSMutableArray!

* Mar. 15th, 2003 - implemented ChrisTh's offline suggestions re: method prototypes (to reduce the number of calls to +signatureWithObjCTypes:) and the +(id)trampolineWithTarget:selector:prototypeSelector: factory method. Also formatted to help remove the HorizontalScrollbars.

* Apr. 08th, 2003 - implemented changes in LSTrampoline allowing overall returns other than objects. This is demonstrated by sumInt and sumFloat which will return the sum of int- or float-returns, respectively. Another potential use (although one which would need a bit of an addition to -forwardInvocation: to handle overall BOOL-returns--not a complex matter at all) is adding BOOL values together to see if all objects in a collection return the same, YES or NO, to the message in question; just boolean compare against 0 or -count, respectively.

In short, if you want to assign [[array message] higherOrderMessage] to something other than id, these changes are for you.

Also added was -detect; this returns the first object returning YES to the higher-order message.

Finally, wiki-directed prettification (formatting of code) was done once again.

