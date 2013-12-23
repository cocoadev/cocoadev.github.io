---
layout: page
title: ObjectiveMethod
---

FDObjectiveMethod is a class which allows you to treat a method as an object. It's not just an invocation, because it runs the invocation you pass it in another thread, and allows you to start, halt, and restart the progress of the method referenced to by the invocation you pass the FDObjectiveMethod.

What that means is that FDObjectiveMethod provides you with very easy multithreading for worker threads. It is not suitable for splitting off a thread that will need to do a lot of communicating, but that's something I'm looking into.

Class reference:

* -(id)initWithInvocation:(NSInvocation *)anInvocation

This is the designated initializer (in fact, currently, it's the only one) for FDObjectiveMethod. You provide it with a complete NSInvocation, and your object is initialized.

* -(void)execute

Use this method to start the method running. If it is already running, nothing happens.

* -(void)stopExecution

Use this method to halt the execution of the method. If it is not running, nothing happens.

* -(NSInvocation *)invocation

Returns the invocation that the receiver was initialized with.

* -(void)setInvocation:(NSInvocation *)newInvocation

Sets the invocation that the receiver should use. Not yet thread-safe, but that's not too hard; just a matter of locking (yes, I know, it should be thread-safe on its own...).

* -(NSConnection *)executionConnection

Returns the connection that the receiver uses to handle the spawned thread.

* -(BOOL)isRunning

Returns YES if the method is currently executing, NO otherwise.


----

I have provided the code for your fancy (this is newer than the code on my site).

**From FDObjectiveMethod.h**

    
//
//  FDObjectiveMethod.h
//  FDFoundation
//
//  Created by Rob Rix on Thu Jun 28 2001.
//  Copyright (c) 2001 Rob Rix. All rights reserved.
//

//	v1.2

#import <Foundation/Foundation.h>

@interface FDObjectiveMethod : NSObject {
	NSConnection *executionConnection;
	NSInvocation *theInvocation;
	
	BOOL isRunning;
	
	id privateMethodObject; // don't mess with me!
}
-(id)initWithInvocation:(NSInvocation *)anInvocation;
-(void)dealloc;

-(void)execute; // invokes the invocation in a new thread
-(void)stopExecution; // stops the new thread (and therefore the invocation)

-(NSConnection *)executionConnection;
-(void)setInvocation:(NSInvocation *)newInvocation;
-(NSInvocation *)invocation;
-(BOOL)isRunning;
@end


----

**From FDObjectiveMethod.m**

    
//
//  FDObjectiveMethod.m
//  FDFoundation
//
//  Created by Rob Rix on Thu Jun 28 2001.
//  Copyright (c) 2001 Rob Rix. All rights reserved.
//

//	v1.2

#import "FDObjectiveMethod.h"

// private, for internal use only, et cetera. Do not mess with this. Use FDObjectiveMethod instead
@interface __FDMethodController : NSObject
-(id)init;
-(void)dealloc;

-(void)stop;
@end

// private helper class implementation
@implementation __FDMethodController

-(id)init
{
	return self = [super init];
}

-(void)dealloc
{
	[super dealloc];
}

-(void)stop
{
	[[NSConnection defaultConnection] invalidate];
}

@end

// the real implementation!

@implementation FDObjectiveMethod

-(id)initWithInvocation:(NSInvocation *)anInvocation
{
	theInvocation = [anInvocation retain];
	isRunning = NO;
	
	return self = [super init];
}

-(void)dealloc
{
	[theInvocation release];
	if(isRunning == YES)
		[self stopExecution];
	
	[super dealloc];
}

-(void)execute
{
	if(isRunning == NO)
	{
		NSPort *port1;
		NSPort *port2;
		
		port1 = [NSPort port];
		port2 = [NSPort port];
		
		isRunning = YES;
		
		executionConnection = [[NSConnection alloc] initWithReceivePort:port1
															   sendPort:port2];
		[executionConnection setRootObject:self];
		
		[NSThread detachNewThreadSelector:@selector(__invokeInstance:)
								 toTarget:[self class]
							   withObject:self];
	}
}

-(void)stopExecution
{
	if(isRunning == YES)
	{
		isRunning = NO;
		
		[privateMethodObject stop];
		
		[executionConnection release];
		executionConnection = nil;
	}
}

-(NSConnection *)executionConnection
{
	return executionConnection;
}

-(void)setInvocation:(NSInvocation *)newInvocation
{
	[newInvocation retain];
	[theInvocation release];
	theInvocation = newInvocation;
}

-(NSInvocation *)invocation
{
	return theInvocation;
}

-(BOOL)isRunning
{
	return isRunning;
}

// private, hence the double underscore prefix

-(void)__setPrivateMethodObject:(id)newPrivateMethodObject
{
	[newPrivateMethodObject retain];
	[privateMethodObject release];
	privateMethodObject = newPrivateMethodObject;
}

+(void)__invokeInstance:(FDObjectiveMethod *)theInstance
{
	NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
	NSConnection *serverConnection = [NSConnection connectionWithReceivePort:theInstance executionConnection] sendPort] sendPort:[[theInstance executionConnection] receivePort;
	__FDMethodController *serverObject = __FDMethodController alloc] init];
	
	[(id)[serverConnection rootProxy] __setPrivateMethodObject:serverObject];
	[serverObject release];
	
	[[theInstance invocation] invoke];
	
	[[[[NSRunLoop currentRunLoop] run];
	
	[pool release];
	[NSThread exit];
}

@end


-- RobRix

