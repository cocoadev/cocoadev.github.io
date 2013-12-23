---
layout: page
title: ThreadCommunication
---



A simple General/WorkerThread can be managed with General/NSObject's     performSelectorOnMainThread:withObject:waitUntilDone:. Arbitrary communication can be handled with General/DistributedObjects (see General/DistributedObjectsSampleCode, or http://lachand.free.fr/cocoa/Threads.html ), or with the libraries that other industrious people have written.

Two good ones are General/InterThreadMessaging (which allows notifications and method invocations on arbitrary threads) and General/ThreadWorker (which executes a method on a separate thread and invokes a callback when done).

Some other solutions are below, and at the General/ObjectLibrary page.

----

Here's a simple technique for communicating between threads.

See General/ThreadWorker instead. It encapsulates the technique below into a single method. Use the technique below if you have more than a 'finish' method to invoke.

Let's say you have an object for which you wish to launch a worker thread (workerThread:) and have some method (workerThreadFinished) invoked when the worker thread finishes.

The interface might be:

    
@interface General/MyObject
{
	General/NSConnection *_connection;
}

- (void)workerThread:(General/NSArray *)ports;

- (void)workerThreadFinished;

@end


An implementation might be:

    
@implementation General/MyObject

- (void)dealloc
{
	[_connection release];
}

- (void)workerThread:(General/NSArray *)ports
{
    General/NSConnection *connection;
    General/NSAutoreleasePool *pool = General/[[NSAutoreleasePool alloc] init];

    connection = General/[NSConnection connectionWithReceivePort:[ports objectAtIndex:0]
                                                sendPort:[ports objectAtIndex:1]];

    NS_DURING
    {
		// do my work

		[(General/MyObject *)[connection rootProxy] workerThreadFinished];
    }
    NS_HANDLER
    {
    }
    NS_ENDHANDLER

    [pool release];
}

- (void)workerThreadFinished
{
	// this will happen in the main thread
}

- (void)doLaunchThread
{
    General/NSPort *receive_port = General/[NSPort port];
    General/NSPort *send_port = General/[NSPort port];

    General/NSArray *ports = General/[NSArray arrayWithObjects:send_port, receive_port, NULL];

    _connection = General/[[NSConnection alloc] initWithReceivePort:receive_port sendPort:send_port];
    [_connection setRootObject:self];

	General/[NSThread detachNewThreadSelector:@selector(workerThread:) toTarget:self withObject:ports];	
}


The nice thing about this code is that you can send any message to the main thread as long as you do it through the root proxy.

It's all done using General/DistributedObjects (non-public ones). General/ThreadWorker illustrates another way to notify the main thread of worker thread completion without using General/NSConnection or General/DistributedObjects.

----
In 10.2 General/NSObject gained a <code>- (void)performSelectorOnMainThread:(SEL)aSelector withObject:(id)arg waitUntilDone:(BOOL)wait</code>. General/NSNotification is also thread-safe and may be of use, but see General/NotificationsAcrossThreads

----

An extremely simple solution by General/JonathanJansson.

In the work for on a port of DC++ to Mac OS X (on General/SourceForge) I needed a functions similar to General/PostMessage() in Windows. General/PostMessage() sends a message to the thread owning a window. This code is implemented as a category to General/NSObject and does not make any use of General/NSPort! The code sends a message that are taken care of in the main thread. An General/NSAutoreleasePool should be in place because of the way an General/NSInvocation is created. The General/NSInvocation also retain its objects because the sending side will probably release them before the message is actually delivered. 

A correction. It is not necesary to retain the invocation, performSelectorOnMainThread:withObject:waitUntilDone: retains both the argument and receiver and releases them when done. This technique is almost pointless if ony one argument is used. It only removes the need to specify waitUntilDone: .

It should be easy to send the handleInvocation: message to any run loop by using -General/[NSRunLoop performSelector:target:argument:order:modes:] on that run loop instead.

    
@implementation General/NSObject ( General/RunLoopMessenger )

- (void)mainPerformSelector:(SEL)aSelector withObject:(id)argument1 withObject:(id)argument2
{
	General/NSInvocation *invocation;
	invocation = General/[NSInvocation invocationWithMethodSignature:[self methodSignatureForSelector:aSelector]];
	
	[invocation setSelector:aSelector];
	[invocation setArgument:&argument1 atIndex:2];
	[invocation setArgument:&argument2 atIndex:3];
	[invocation retainArguments];

	[self performSelectorOnMainThread:@selector(handleInvocation:) withObject:invocation waitUntilDone:NO];
}

- (void)handleInvocation:(General/NSInvocation *)anInvocation
{
	[anInvocation invokeWithTarget:self];
}

@end


----

General/CHOMp (and I'm sure other HOM implementations) could be very easily extended to allow cross-thread invocation of arbitrary method calls. Using it would look like this:
    
[[obj performOnMainThreadWaitUntilDone:YES] complicatedMessageWithObject:foo withInt:bar withFloat:baz];

This would be a lot like DO, but easier to set up and more specific in what it can do. Anybody interested, or shall I go back to my cave? ;-)
