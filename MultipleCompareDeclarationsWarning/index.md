---
layout: page
title: MultipleCompareDeclarationsWarning
---

Take a simple program like this:
    
/* main.m */
#import <Cocoa/Cocoa.h>

int main ()
{
   id a = nil, b = nil;
   [a compare:b];
   return 0;
}


It outputs the following warnings:
    
main.m: In function `main':
main.m:7: warning: multiple declarations for method `compare:'
/System/Library/Frameworks/Foundation.framework/Headers/NSValue.h:53: warning: using `-(NSComparisonResult)compare:(NSNumber *)otherNumber'
/System/Library/Frameworks/Foundation.framework/Headers/NSDate.h:29: warning: also found `-(NSComparisonResult)compare:(NSDate *)other'
/System/Library/Frameworks/Foundation.framework/Headers/NSString.h:74: warning: also found `-(NSComparisonResult)compare:(NSString *)string'


Is there anyway to suppress this crap, without having to specifically provide a type for id?

----

The easiest answer is to add a category on NSObject specifying a generic -compare:, i.e.
    
@interface NSObject (UniversalCompare)
- (NSComparisonResult)compare:(id)otherObj;
@end

You don't even need to provide an implementation; just providing the interface is enough to satisfy the compiler.  -- Bo

*That's a great idea, Bo. Now if only I could reconcile     -(void)draw and     -(BOOL)draw by that means...*

----

Unfortunately it does **not** work :( If I let     a and     b be     NSObject*, then the warnings are left out, but for     id they remain.

----

Shucks.  I have to admit I hadn't tried it under quite those circumstances, though it did seem to work in situations where you were taking an object straight out of a collection (e.g.     -objectAtIndex:) and calling     -compare: on it, and I thought those returned the     id type.  Anyway, I guess you could cast it, i.e.     [(NSObject*)a compare:b] which is pretty ugly but gets the job done.  -- Bo

I'm pretty sure the only way to fix that is either to make the objects be a definite class (NSObject?), or to cast them for comparison.  Why don't you just make them NSObjects?  You know that an NSObject* can in fact be any object that inherits from NSObject, right?  --OwenAnderson

Yes, I do know about inheritance ;-)

