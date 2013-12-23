---
layout: page
title: MultiProcessorWorkSharing
---

Wondering how to take advantage of multiple processor macs in a systematic, simple way? Want some fine-grained parallelism in your app?

Here are some classes and protocols to help you achieve it. Any class implementing the protocol Schedulable can be scheduled on multiple processors by a Scheduler object. A Schedulable object must break the work it needs done up into a number of work units, which must be independent of one another. The Scheduler will create the threads required, and calls to the Schedulable object requesting that it perform certain work units in each thread.

An example of how you can use these code units is given at the end.

DrewMcCormack


----

See also AtomicSQ, ProducersAndConsumerModel, and ThreadWorker.

----


    

------------ Schedulable.h ---------------

#import <Foundation/Foundation.h>
#import "Scheduler.h"

@protocol Schedulable 
-(void)performWorkUnits:(NSSet *)workUnits forScheduler:(Scheduler *)scheduler;
@end


------------- Scheduler.h ----------------

#import <Foundation/Foundation.h>


@protocol Schedulable;


@interface Scheduler : NSObject {
    @private
    id _delegate;				// Delegate
    id <Schedulable> _schedulableObject;	// Object which has work units to be scheduled
    NSMutableSet *_workUnitsRemaining;		// Work units not yet performed in schedule
    NSLock *_remainingUnitsLock;		// Lock to keep the remaining work units set consistent
    BOOL _scheduleWasCancelled; 		// Flag set when schedule is cancelled
    unsigned _numberOfThreads;			// Number of simultaneous threads used to perform work
    unsigned _numberOfDetachedThreads;		// The current number of worker threads detached.
}

-(id)initForSchedulableObject:(id <Schedulable>)schedObj andNumberOfThreads:(unsigned)numThreads;
-(id)initForSchedulableObject:(id <Schedulable>)schedObj;
-(void)dealloc;

-(void)performScheduleForWorkUnits:(NSSet *)workUnits;
-(void)cancelSchedule;

// Template method. Overload in subclasses
-(NSSet *)_workUnitsToExecuteForRemainingUnits:(NSSet *)remainingUnits;

// Accessors
-(unsigned)numberOfThreads;

-(id)delegate;
-(void)setDelegate:(id)delegate;

-(id <Schedulable>)schedulableObject;

-(NSMutableSet *)_workUnitsRemaining;

@end


@interface Scheduler (SchedulerDelegateMethods)

// Sent in the main thread
-(void)schedulerWillBeginSchedule:(Scheduler *)sender;

// The following are sent in the worker thread
-(BOOL)scheduler:(Scheduler *)scheduler shouldBeginUnits:(NSSet *)units;
-(void)scheduler:(Scheduler *)scheduler didCompleteUnits:(NSSet *)units;

// Sent in the main thread
-(void)schedulerDidCancelSchedule:(Scheduler *)scheduler;
-(void)schedulerDidFinishSchedule:(Scheduler *)scheduler;

@end



----------------- Scheduler.m ---------------------

#import "Scheduler.h"
#import "Schedulable.h"

#define DEFAULT_NUMBER_OF_THREADS 2


@interface Scheduler (PrivateMethods)
-(NSSet *)_getNextUnitsToPerform;
-(void)_setNumberOfThreads:(unsigned)numThreads;
-(void)_setSchedulableObject:(id <Schedulable>)schedObj;
-(void)_setWorkUnitsRemaining:(NSMutableSet *)unitsRemaining;
@end


@implementation Scheduler


-(id)initForSchedulableObject:(id <Schedulable>)schedObj andNumberOfThreads:(unsigned)numThreads {
    if ( self = [super init] ) {
        [self _setSchedulableObject:schedObj];
        [self _setNumberOfThreads:numThreads];
        _remainingUnitsLock = [[NSLock alloc] init];
    }
    return self;
}


-(id)initForSchedulableObject:(id <Schedulable>) schedObj {
    return [self initForSchedulableObject:schedObj andNumberOfThreads:DEFAULT_NUMBER_OF_THREADS];
}


-(void)dealloc {
    [_schedulableObject release];
    [_workUnitsRemaining release];
    [_remainingUnitsLock release];
    [super dealloc];
}
    

-(void)performScheduleForWorkUnits:(NSSet *)workUnits {
    id <Schedulable> schedObj = [self schedulableObject];
    
    NSAssert( nil != schedObj, @"Schedulable object nil in performScheduleForWorkUnits:" );
    
    // Keep track of which units are still left to perform
    [self _setWorkUnitsRemaining:[workUnits mutableCopy]];
    
    // Set the cancellation flag
    _scheduleWasCancelled = NO;
    
    // Inform delegate that we are beginning
    if ( [_delegate respondsToSelector:@selector(schedulerWillBeginSchedule:)])
        [_delegate schedulerWillBeginSchedule:self];

    // Start each thread
    _numberOfDetachedThreads = 0;
    unsigned threadIndex;
    for ( threadIndex = 0; threadIndex < [self numberOfThreads]; ++threadIndex ) {
        _numberOfDetachedThreads++;
        [NSThread detachNewThreadSelector:@selector(_performWorkUnitsInWorkerThread)
            toTarget:self 
            withObject:nil];
    }
    
}


-(void)_performWorkUnitsInWorkerThread {
    NSSet *nextUnits = nil;
    BOOL performNextUnits;
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];

    while ( ( nextUnits = [self _getNextUnitsToPerform] ) && !_scheduleWasCancelled ) {
        
        // Inform delegate that new units will be performed. Also give chance to veto.
        performNextUnits = YES;
        if ( [_delegate respondsToSelector:@selector(scheduler:shouldBeginUnits:)] )
            performNextUnits = [_delegate scheduler:self shouldBeginUnits:nextUnits];

        if ( performNextUnits ) {
            self schedulableObject] performWorkUnits:nextUnits forScheduler:self];
            
            // Inform delegate of completion
            if ( [_delegate respondsToSelector:@selector(scheduler:didCompleteUnits:)] )
                [_delegate scheduler:self didCompleteUnits:nextUnits];
                
        }
        
    }
    
    // Inform the main thread that this worker thread is finished its units.
    [self performSelectorOnMainThread:@selector(_didFinishPerformingWorkUnits)
        withObject:nil waitUntilDone:NO];
    
    [pool release];
    
}


// Returns the next units to perform, and removes them from the work units remaining set.
// This operation is threadsafe.
-([[NSSet *)_getNextUnitsToPerform {
    NSSet *nextUnits = nil;
    
    // Need to lock here because a race condition could arise for the work units
    // remaining set.
    [_remainingUnitsLock lock];
    nextUnits = [self _workUnitsToExecuteForRemainingUnits:[self _workUnitsRemaining]];
    self _workUnitsRemaining] minusSet:nextUnits];
    [_remainingUnitsLock unlock];
    
    return ( 0 == [nextUnits count] ? nil : nextUnits );
}


-(void)_didFinishPerformingWorkUnits {

    _numberOfDetachedThreads--;
        
    // If all workers are finished, inform the delegate of successful completion or cancellation.
    if ( 0 == _numberOfDetachedThreads ) {
        if ( !_scheduleWasCancelled &&
            [_delegate respondsToSelector:@selector(schedulerDidFinishSchedule:)] ) {
            [_delegate schedulerDidFinishSchedule:self];
        }
        else if ( _scheduleWasCancelled &&
            [_delegate respondsToSelector:@selector(schedulerDidCancelSchedule:)] ) {
            [_delegate schedulerDidCancelSchedule:self];
        }
    }

}


-(void)cancelSchedule {
    _scheduleWasCancelled = YES;
}


// Abstract method. Returns nil if there are no more units to complete.
-([[NSSet *)_workUnitsToExecuteForRemainingUnits:(NSSet *)remainingUnits {
    [self doesNotRecognizeSelector:_cmd];
    return nil;
}


// Accessors
-(void)_setNumberOfThreads:(unsigned)numThreads {
    _numberOfThreads = numThreads;
}


-(unsigned)numberOfThreads {
    return _numberOfThreads;
}


-(id)delegate {
    return _delegate;
}


-(void)setDelegate:(id)delegate {
    _delegate = delegate;
}


-(void)_setSchedulableObject:(id <Schedulable>)schedObj {
    [schedObj retain];
    [_schedulableObject release];
    _schedulableObject = schedObj;
}


-(NSObject <Schedulable> *)schedulableObject {
    return _schedulableObject;
}


-(void)_setWorkUnitsRemaining:(NSMutableSet *)unitsRemaining {
    [unitsRemaining retain];
    [_workUnitsRemaining release];
    _workUnitsRemaining = unitsRemaining;
}


-(NSMutableSet *)_workUnitsRemaining {
    return _workUnitsRemaining;
}


@end



------------------- StaticScheduler.h --------------------

#import <Foundation/Foundation.h>
#import "Scheduler.h"


@interface StaticScheduler : Scheduler {
    @private
    int _numberOfThreadsLeft; // Used to keep track of how many threads already have work
}

-(void)performScheduleForWorkUnits:(NSSet *)workUnits;
-(NSSet *)_workUnitsToExecuteForRemainingUnits:(NSSet *)remainingUnits;

@end


-------------------- StaticScheduler.m ---------------------

#import "StaticScheduler.h"


@implementation StaticScheduler


-(void)performScheduleForWorkUnits:(NSSet *)workUnits {
    _numberOfThreadsLeft = [self numberOfThreads];
    [super performScheduleForWorkUnits:workUnits];
}


// Divide units as equally as possible between threads. 
-(NSSet *)_workUnitsToExecuteForRemainingUnits:(NSSet *)remainingUnits {
    int numUnitsLeft = [remainingUnits count];
    int numUnitsThisThread = numUnitsLeft / _numberOfThreadsLeft + 
        ( 0 != numUnitsLeft % _numberOfThreadsLeft ? 1 : 0 ); // Add 1 if doesn't divide exactly
    _numberOfThreadsLeft--;
    return [NSSet setWithArray:
        remainingUnits allObjects] subarrayWithRange:[[NSMakeRange(0, numUnitsThisThread)]];
}


@end



--------------------- DynamicScheduler.h -------------------------

#import <Foundation/Foundation.h>
#import "Scheduler.h"


@interface DynamicScheduler : Scheduler {

}

-(NSSet *)_workUnitsToExecuteForRemainingUnits:(NSSet *)remainingUnits;

@end



----------------------- DynamicScheduler.m ------------------------

#import "DynamicScheduler.h"


@implementation DynamicScheduler


// Divide units as equally as possible between threads. 
-(NSSet *)_workUnitsToExecuteForRemainingUnits:(NSSet *)remainingUnits {
    return ( [remainingUnits count] == 0 ? [NSSet set] :
        [NSSet setWithArray:remainingUnits allObjects] subarrayWithRange:[[NSMakeRange(0, 1)]] );
}


@end


----------------------- Example of Work Sharing---------------------
// In header

@interface ExampleWorkClass : NSObject <Schedulable>

...

// In implementation file

    // Create a scheduler
    id sched = [DynamicScheduler initForSchedulableObject:self andNumberOfThreads:4];
    
    // Create the work units. These can be anything. We will use NSNumbers
    unsigned i;
    NSMutableSet *unitsSet = [NSMutableSet set];
    for ( i = 0; i < numWorkUnits; ++i ) {
        [unitsSet addObject:[NSNumber numberWithInt:i]];
    }
    
    // Perform work schedule
    [sched performScheduleForWorkUnits:unitsSet];


... 

// Also in implementation file

-(void)performWorkUnits:(NSSet *)workUnits forScheduler:(Scheduler *)scheduler {

    // Do work here for the set of units passed. This is from the Schedulable protocol

}




