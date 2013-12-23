---
layout: page
title: CSCoroutineTest
---

This is a small test for CSCoroutine. It simply runs two interleaved loops that count up and down.

----

Update on September 12, 2007: Added a second test for thread-switching.

Update on September 20, 2007: Changed code to use NSConditionLock.

Update on October 10, 2007: Added an exception-throwing test, and comments.

----

    
#import "CSCoroutine.h"

@interface TestObject:NSObject
{
	NSConditionLock *lock;
}
-(void)test;
-(void)test1:(int)val;
-(void)test1b:(int)val;
-(void)test2;
-(void)test2b;
-(void)test3;
-(void)test3b:(CSCoroutine *)coro;
-(void)test3c;
@end

@implementation TestObject

-(void)test
{
	// Run each test
	[self test1:10];
	[self test2];
	[self test3];
}

-(void)test1:(int)val
{
	printf("Test 1: Interleaved loops\n");

	// Create and start coroutine which will return immediately.
	CSCoroutine *coro=[self newCoroutine];
	[(id)coro test1b:val];

	// Print five numbers, invoking the coroutine after each.
	for(int i=0;i<=5;i++)
	{
		printf("a: %d\n",val+i);
		[coro switchTo];
	} 

	[coro release];
}

-(void)test1b:(int)val
{
	// Return immediately to let main function run.
	[CSCoroutine returnFromCurrent];

	// Print five numbers, returning after each one
	for(int i=0;i<=5;i++)
	{
		printf("b: %d\n",val-i);
		[CSCoroutine returnFromCurrent];
	}
}

-(void)test2
{
	printf("Test 2: Exceptions\n");

	// Save the current coroutine pointer before calling potentially
	// exception-throwing coroutine.
	CSCoroutine *savedcoro=[CSCoroutine currentCoroutine];

	@try
	{
		// Create and start coroutine which will throw an exception.
		CSCoroutine *coro=[self newCoroutine];
		[(id)coro test2b];
	}
	@catch(id e)
	{
		// Clean up after the exception by explicitly restoring
		// the current coroutine pointer. This is important!
		[CSCoroutine setCurrentCoroutine:savedcoro];
		printf("Exception caught!\n");
	}
}

-(void)test2b
{
	printf("In coroutine, throwing exception.\n");
	[NSException raise:@"TestException" format:@"Test"];
}

-(void)test3
{
	printf("Test 3: Jumping threads\n");

	// Create and start a coroutine on the main thread.
	CSCoroutine *coro=[self newCoroutine];
	[(id)coro test3c];

	// Detach a secondary thread, which will also invoke the same coroutine.
	lock=[[NSConditionLock alloc] initWithCondition:0];
	[NSThread detachNewThreadSelector:@selector(test3b:) toTarget:self withObject:coro];

	// Wait until thread finishes.
	[lock lockWhenCondition:1];
	[lock unlock];
	[lock release];
}

-(void)test3b:(CSCoroutine *)coro
{
	printf("Separate thread started\n");

	// Invoke the coroutine created earlier, then clean up and exit.
	[coro switchTo];
	[coro release];
	[lock lockWhenCondition:0];
	[lock unlockWithCondition:1];
}

-(void)test3c
{
	printf("First invocation in main thread.\n");
	[CSCoroutine returnFromCurrent];
	printf("Second invocation in separate thread.\n");
}

@end

int main(int argc,char **argv)
{
	NSAutoreleasePool *pool=[NSAutoreleasePool new];

	TestObject *test=[TestObject new];
	[test test];
	[test release];

	[pool release];
}


The correct output should look something like this:

    
Test 1: Interleaved loops
a: 10
b: 10
a: 11
b: 9
a: 12
b: 8
a: 13
b: 7
a: 14
b: 6
a: 15
b: 5
Test 2: Exceptions
In coroutine, throwing exception.
Exception caught!
Test 3: Jumping threads
First invocation in main thread.
Separate thread started
Second invocation in separate thread.

