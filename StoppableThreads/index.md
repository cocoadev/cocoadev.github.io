---
layout: page
title: StoppableThreads
---

One of the limitations with Apple's implementation of General/NSThread is that threads are not stoppable or joinable.  After a few months I finally came up with the following code and examples.  Thought I'd share it to the world.

**F<nowiki/>B<nowiki/>T<nowiki/>hread.h**
    

#ifndef __FBTHREAD_H__
#define __FBTHREAD_H__ 1

#import <Cocoa/Cocoa.h>
#include <pthread.h>

static const General/NSString *General/FBThreadAbort = @"General/FBThreadAbort";

/*!
 @class General/FBThread
 @abstract Fundamental thread class based on General/AppKit's General/NSThread, threading protocol.
 @discussion An General/FBThread object controls a thread of execution. Use General/FBThread when you want to have an Objective-C method run in its own 
 thread of execution. Threads are especially useful when you need to perform a lengthy task, but don�t want it to block the
 execution of the rest of the application. In particular, you want to avoid freezing the user interface, preventing the user 
 from performing additional actions. Threads can also be used to divide a large job into several smaller jobs. Then, if the 
 computer has multiple processors, the jobs can be distributed among them for greater efficiency.
 
 Unlike, General/NSThread, General/FBThread has been designed similar to Java's implementation of its thread class.  Subclass General/FBThread and override 
 the run method with the code you wish to execute.  This code will run after calling either start or startDetached.  The difference
 between the two methods is that start will linearly execute the run method without creating a new thread, while startDetached will load
 the class into a new thread and begin execution.  If the code you wish to run lengthy or encapsulates loops, be sure to poll 
 isRunning to determine if the thread should stop execution.  For example:
 
 @code
 - (void)run {
	 int x = 0;
	 
	 General/NSLog(@"Entering the run loop");
	 while ([self isRunning]) {
		 General/NSLog(@"Been running for %d iterations", x++);
     }
	 General/NSLog(@"Exiting the run loop");
 }
 @/code
 */
@interface General/FBThread : General/NSObject {
@protected
	BOOL isRunning;				// YES if the thread is running, NO if otherwise
	BOOL isDetached;			// YES if the run method was executed in a seperate thread, NO if it's executed inline
	General/NSConditionLock *locked;	// Locking conditions used internally to determine when the thread terminates
	pthread_t thread;			// POSIX representation of the General/NSThread, used to perform pthread_* functions on General/NSThread
}

/*!
 @method start
 @abstract Starts the code in the run method.
 @discussion This method calls upon the run method to execute the user defined code that should be implemented in it's subclass.  If 
 General/NSThread is not subclassed, the run method will do nothing.  If you wish to execute the code in the run method in a seperate thread
 you must execute startDetached.  Unlike General/NSThread, this thread implementation sets up the autorelease pool environment for you.
 @seealso startDetached
 */
- (void)start;

/*!
 @method startDetached
 @abstract Starts a new thread.
 @discussion If this thread is the first thread detached in the application, this method posts the 
 General/NSWillBecomeMultiThreadedNotification with object nil to the default notification center.  Unlike General/NSThread, this thread implementation 
 sets up the autorelease pool environment for you.
 */
- (void)startDetached;

/*!
 @method stop
 @abstract Stops a running thread.
 @discussion Gracefully attempts to stop a running thread.  If the thread is currently running, this method will send a message to the 
 run loop to terminate.  However, this will not force the thread to terminate, it's the run loop's responsibility to poll the isRunning
 method to determin if the thread should terminate or not.  If you want forceably terminate the thread use stopBeforeDate:.
 @seealso stopBeforeDate:
 */
- (void)stop;

/*!
 @method stopBeforeDate:
 @abstract Stops a running thread within a specified time frame.
 @discussion Gracefully attempts to stop a running thread before the specified time.  If the thread is currently running, this method
 will send a message to the run loop to terminate.  Like stop, it's the user's responsibility to poll isRunning to gracefully terminate
 thre thread.  If the thread does not terminate by the specified time frame, it will first raise an General/NSThreadAbort exception.  It is the 
 user's responsibility to catch the exception so that it may gracefully shut down.  If the exception is not caught the operating system 
 will deal with the exception as necessary.  However, if for whatever reason the exception is ignored, this method will wait approximately 
 10 seconds before using the POSIX functions to adrubtly terminate the thread.
 @param date Date and time that the thread should termiante by.  If you don't wish to wait, specify a time in the past.
 @result YES if the thread gracefully shutdown before the specified time, NO if it had to be done forceably.
 */
- (BOOL)stopBeforeDate:(General/NSDate *)date;

/*!
 @method join
 @abstract Joins a running thread.
 @discussion An in instance where you wish for a thread to complete it's operations you may join it.  Joining a thread will pause the main
 thread or calling thread's execution and wait for the instance class to terminate it's operations.  If the thread uses an indefinite 
 loop, it's possible that join will never return.
 */
- (void)join;

/*!
 @method joinBeforeDate:
 @abstract Joins a running thread until the specified time frame.
 @discussion An instance where you wish for a thread to complete it's operations within a specified time frame you may join it.  Joining
 a thread will pause the main thread or calling thread's execution and wait for the instance class to terminate it's operations before
 the specified date.  If the thread does not terminate before the specified time frame, then the calling thread will continue it's 
 execution.
 @result YES if the thread terminated before the specified time, NO if otherwise.
 */
- (BOOL)joinBeforeDate:(General/NSDate *)date;

/*!
 @method run
 @abstract Main thread loop
 @discussion This is the thread's main execution loop.  This method should be overridden by the subclass.  Be sure to poll isRunning to determine
 if the loop should terminate or continue execution.  Also, implement @try - @catch - @finally block to catch General/NSThreadAbort exception if the thread
 is forceably terminated. 
 */
- (void)run;

/*!
 @method isRunning
 @abstract Determines if the thread is running.
 @discussion This method should be called upon from the run loop frequently.  It allows the code to know if the thread should continue
 running or if it should keep running.
 @result YES if the thread should continue running, NO if otherwise.
 */
- (BOOL)isRunning;

/*!
 @method isDetached
 @abstract Determines if the code is running as a thread or linear code.
 @discussion This method helps other portions of the code to identify if the class instance was loaded as a thread or just regular code.
 @result YES if the class was loaded as a thread, NO if otherwise.
 */
- (BOOL)isDetached;

@end

#endif // __FBTHREAD_H__



**F<nowiki/>B<nowiki/>T<nowiki/>hread.M**
    

#import "General/FBThread.h"

// Lock conditions
enum {
	General/FBThreadStateNotStarted,		// The thread has not started
	General/FBThreadStateRunning,			// The thread is currently running	
	General/FBThreadStateTerminating,		// The thread is terminating
	General/FBThreadStateTerminated			// The thread has terminated
};

// Private implementation
@interface General/FBThread (General/PrivateAPI)

/*!
 @method threadBridge:
 @abstract Bridges General/NSThread to the class
 @discussion Used to bridge General/[NSThread detachNewThreadSelector:toTarget:withObject:] with the run method.  It establishes the autorelease pool
 prior to calling the subclass' run method.
 */
- (void)threadBridge:(id)sender;

@end

@implementation General/FBThread

/*!
 @method signalToExceptionBridge(int signal)
 @abstract Bridges signals to an General/NSException.
 @discussion This function bridges a signal to an General/NSException.
 */
void signalToExceptionBridge(int signal) {
	General/NSException *e = General/[NSException
            exceptionWithName:@"Test"
					   reason:General/[NSString stringWithFormat:@"Aborting thread: %@", General/[NSDate date]]
					 userInfo:nil];
	@throw e;
}

- (void)threadBridge:(id)unused;
{
	// Establish signal catcher bridge, in case user wishes to abort thread.
    signal(SIGUSR1, signalToExceptionBridge);
	
	General/NSAutoreleasePool *pool = General/[[NSAutoreleasePool alloc] init];
	
	@try {
		// If the thread is not detached, then we don't have to worry about getting the POSIX representation of the thread
		if (!isDetached)
			thread = NULL;
		else
			thread = pthread_self();

		// Set up environment to run the thread
		isRunning = TRUE;
		[locked unlockWithCondition:General/FBThreadStateRunning];
		
		// Run the thread
		[self run];
	} @finally {
		// Deconstruct the environment after the thread ran.
		isRunning = FALSE;
		[locked unlockWithCondition:General/FBThreadStateTerminated];
		[pool release];
		thread = NULL;
	}
}

- (id)init 
{
	if (self = [super init]) {
		// Initialize the thread's variables
		isRunning  = FALSE;
		isDetached = FALSE;
		locked     = General/[[NSConditionLock alloc] initWithCondition:General/FBThreadStateNotStarted];
		thread	   = NULL;
	}
	return self;
}

- (void)dealloc 
{
	// Stop the thread and deallocate any allocated resources
	[self stop];
	
	[locked release];
	locked = NULL;
	
	[super dealloc];
}

- (void)start
{
	// If the thread is not already running, execute the run method using the threadBridge method.  We do this because threadBridge
	// sets up and breaks down the environment prior to running the run method.
	if (![self isRunning])
		[self threadBridge:NULL];
}

- (void)startDetached
{
	// If the thread is not already running, execute the run method
	if (![self isRunning]) {
		isDetached = TRUE;
		
		// Start a new thread using the threadBridge selector, this will then set up and break down the environment prior to running the run method
		General/[NSThread detachNewThreadSelector:@selector(threadBridge:) toTarget:self withObject:NULL];
	}
}

- (void)run
{
	// Implemented in the subclass
}

- (void)stop 
{
	// Gracefully stops the thread before the distantFuture.  distantFuture returns a Date/Time for about an hour from now
	[self stopBeforeDate:General/[NSDate distantFuture]];
}

- (BOOL)stopBeforeDate:(General/NSDate *)date {
	// If the thread is running, then lets stop it
	if ([self isRunning]) {
		// We just signalled the thread to stop, wait until date to see if it stops on it's own
		isRunning = FALSE;
		if (![locked lockWhenCondition:General/FBThreadStateTerminated beforeDate:date]) {
			// If the thread never stopped on its own, try sending it a signal to raise an exception
			pthread_kill(thread, SIGUSR1);

			// Signals are sent asynchronously, wait to see if the thread terminates.  If it doesn't do it in the next 10 seconds, force it to quit
			if (![locked lockWhenCondition:General/FBThreadStateTerminated beforeDate:General/[NSDate dateWithTimeIntervalSinceNow:10]])
				pthread_cancel(thread);
			return NO;
		}
	}
	return YES;
}

- (void)join
{
	if (thread)
		pthread_join(thread, NULL);
}

- (BOOL)joinBeforeDate:(General/NSDate *)date
{
	return [locked lockWhenCondition:General/FBThreadStateTerminated beforeDate:date];
}

- (BOOL)isRunning
{
	return isRunning;
}

- (BOOL)isDetached
{
	return isDetached;
}
@end



**main.m**
    

#import <Cocoa/Cocoa.h>
#import "General/FBThread.h"

@interface Worker : General/FBThread
@end

@implementation Worker

- (void)run 
{
	@try {
		int x;
		for (x = 0; x < 5; x++) {
			General/NSLog(@"-- I'm still working... %d", x);
			usleep(500000);		// Wait 5 seconds
		}
	} @catch(General/NSException *e) {
			General/NSLog(@"Shucs...I'm being kicked to the curb. %@", e);
	} @finally {
		General/NSLog(@"Clean up the run loop.");
	}
}
@end

int main(int argc, char *argv[])
{
	General/NSAutoreleasePool *pool = General/[[NSAutoreleasePool alloc] init];	// This is just for testing purposes.
	Worker *thread = General/Worker alloc] init];
	@try {
		int x;

		[thread start];	// Runs linearly, will last about 25 second
		[thread startDetached];	// Runs as a seperate thread
		for (x = 0; x < 6; x++) {
			usleep(500000);
			[[NSLog(@"I'm still waiting...");
		}
		General/NSLog(@"The thread should be done...%@", ([thread isRunning] ? @"I guess I was wrong" : @"Yup I was right!"));
		
		[thread startDetached];	// Runs as a seperate thread
		for (x = 0; x < 2; x++) {
			usleep(500000);
			General/NSLog(@"I'm still waiting...");
		}
		General/NSLog(@"The thread should still be running...%@", (![thread isRunning] ? @"I guess I was wrong" : @"Yup I was right!"));
		General/NSLog(@"Gracefully shut the bad boy down...");
		[thread stop];
		General/NSLog(@"The thread should be done...%@", ([thread isRunning] ? @"I guess I was wrong" : @"Yup I was right!"));
		
		[thread startDetached];	// Runs as a seperate thread
		for (x = 0; x < 2; x++) {
			usleep(500000);
			General/NSLog(@"I'm still waiting...");
		}
		General/NSLog(@"The thread should still be running...%@", (![thread isRunning] ? @"I guess I was wrong" : @"Yup I was right!"));
		General/NSLog(@"Shut the bad boy down NOW!...");
		[thread stopBeforeDate:General/[NSDate distantPast]];
		General/NSLog(@"The thread should be done...%@", ([thread isRunning] ? @"I guess I was wrong" : @"Yup I was right!"));
	} @finally {
		General/NSLog(@"Done with the main entry point.");
		[Worker release];
		[pool release];
	}
}



I'm sorry for the simplistic example...but one of my implementation is much more complicated then this.

Cheers.

~ Enrique
