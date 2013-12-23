---
layout: page
title: PerformOnMainThreadTrampoline
---

I got tired of having to build NSInvocation wrappers using the handleInvocation trick as seen in ThreadCommunication. What follows is an implementation for a trivial TrampolineObject that will automatically bounce any message it gets to the main thread.

    
#import "NSObject+Invocations.h"

//////////
@interface EGPerformOnMainThreadTrampoline : NSProxy {
	id object;
	BOOL waitUntilDone;
}

-(id)initWithObject:(id)inobject waitUntilDone:(BOOL)wait;
@end

@implementation EGPerformOnMainThreadTrampoline

-(id)initWithObject:(id)inobject waitUntilDone:(BOOL)wait
{
	object = [inobject retain];
	waitUntilDone = wait;

	return self;
}

-(void)dealloc
{
	[object release];
	[super dealloc];
}

- (void)forwardInvocation:(NSInvocation *)anInvocation 
{
	[anInvocation retainArguments];
	[anInvocation performSelectorOnMainThread:@selector(invokeWithTarget:)
							 withObject:object 
						  waitUntilDone:waitUntilDone];
}

-(NSMethodSignature*)methodSignatureForSelector:(SEL)aSelector
{
    return [object methodSignatureForSelector:aSelector];
}

@end

@implementation NSObject (PerformOnMainThreadTrampoline)

-(NSProxy*)performOnMainThreadWaitUntilDone:(BOOL)wait
{
	return [[[EGPerformOnMainThreadTrampoline alloc] initWithObject:self waitUntilDone:wait] autorelease];
}

@end


    
@interface NSObject (PerformOnMainThreadTrampoline)
-(NSProxy*)performOnMainThreadWaitUntilDone:(BOOL)wait;
@end


You can then send messages like this:

    
object performOnMainThreadWaitUntilDone:NO] this:is a:complex message:0.5f];


Any comments or improvements would be welcome. You can use this code for anything without restriction, but it'd be nice if you'd credit the original author ([[ElliotGlaysher).

----

Also see the implementation of UKKMainThreadProxy from his UKKQueueWatcher distribution.  It's basically the same idea, a transparent category on NSObject that allows you to send any message you want to a given object on the main thread.

JKP

