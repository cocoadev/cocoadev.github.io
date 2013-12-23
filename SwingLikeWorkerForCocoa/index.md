---
layout: page
title: SwingLikeWorkerForCocoa
---

L0Worker is a class that handles working asynchronously while keeping the main thread informed of the progress of the operation (a very common thing to do -- see Finder, General/StuffitExpander, General/DiskUtility...).

To use it, simply subclass it like this:

    

@interface General/SomeWorkerClass : L0Worker { }
@end

@implementation General/SomeWorkerClass

 - (General/NSError*) work {
    /* do some work here, use [self setProgress:...] to send progress messages to the delegate,
    return nil on success or a General/NSError* on failure. */
 }

@end

/* ... somewhere else, in your GUI code ... */

General/SomeWorkerClass* worker = General/[SomeWorkerClass new];
[worker setDelegate:self];
[worker detachWorkerThread];

/* ... let it do its work or call [worker cancel] to stop it ... */

- (void) worker:(L0Worker*) worker didEndWithFinalStatus:(General/NSError*) err canceled:(BOOL) canceled {
    /* called on the main thread, clean up or present the error if err != nil */
}



----

L0Worker.h

    

//  Created by Emanuele on 06/09/05.
//  Copyright 2005 Emanuele Vulcano. Permission granted to everyone to use or modify this
//  code for any purpose.

#import <Cocoa/Cocoa.h>

/*  kL0WorkerIndefiniteProgress
    Signals that it's currently impossible to determine progress information
    for the work. See -setProgress:
*/
#define kL0WorkerIndefiniteProgress (-1.0f)

/*  L0Worker

    A L0Worker instance (or simply a worker) is an object that encapsulates
    code for a time-consuming operation and provides common code to run the
    operation synchronously or asynchronously. A delegate object may be
    provided to receive messages as the operation progresses or concludes itself.
*/
@interface L0Worker : General/NSObject {
    General/NSLock* objectLock;
    
    float progress;
    General/NSString* status;
    BOOL cancelWasInvoked;
    id delegate;
    
    BOOL isSynchronous;
    BOOL isRunning;
}

/*  -work
    To be overridden by subclasses. Executes the time-consuming operation and returns
    a final status in the form of a General/NSError* object (or nil if no error happened).

    You can use -cancelWasInvoked to check whether another thread or the delegate tried
    to cancel the operation and -setProgress: to communicate a new operation progress
    value to the delegate.

    Users of the worker shouldn't call -work directly; instead, -run or -detachWorkerThread
    will set up the worker and call this method synchronously or asynchronously as needed.
*/
- (General/NSError*) work;

/*  -run
    Executes the operation synchronously. In synchronous mode, the -work method will block
    when delegate messages are sent (that is, when a delegate message is sent, operation will
    pause until the delegate method returns).
*/
- (void) run;

/*  -detachWorkerThread
    Detaches a new thread and executes the operation in that thread. Messages sent to the delegate
    during the operation will still be sent on the main thread, not in this new thread.
    Note that, in asynchronous mode, messages sent by the -work method will not cause the operation
    to pause; the -work method will continue executing as delegate messages are received and
    their associated code, if any, is executed on the main thread.
*/
- (void) detachWorkerThread;

/*  -cancelWasInvoked
    Should be called inside of -work only. Returns YES if an outside client called -cancel since
    the beginning of the operation, NO otherwise.
*/
- (BOOL) cancelWasInvoked;

/*  -cancel
    Causes a canceling flag to be activated in the receiver. The -work method can check this
    flag with the -cancelWasInvoked method to terminate the operation, if possible.
    This method can be called:
    * during a synchronous operation, in the code of any delegate message.
    * during an asynchronous operation, by code running in any thread, including the main thread.
*/
- (void) cancel;

/*  -progress, -setProgress:
    Returns and sets the progress value for the operation. -progress can be called to
    read this value from any thread, including the main thread. -setProgress:, instead, should
    not be called by code outside this instance's -work method.

    The progress value must either be kL0WorkerIndefiniteProgress (signaling that
    the progress value is unknown or cannot be computed), or a float value between 0.0f and 1.0f.
    The default progress value after initialization is kL0WorkerIndefiniteProgress.

    The value is only meaningful while the worker is performing the operation (that is, while
    the -isRunning method returns YES).
*/
- (float) progress;
- (void) setProgress:(float) progress;

/*  -delegate, -setDelegate:
    Returns and sets the delegate for this object. Both these methods are thread-safe and can
    be called while the operation is being performed.
*/
- (id) delegate;
- (void) setDelegate:(id) delegate;

/*  -isRunning
    Returns YES as long as the worker is in any of these situations:
    * is preparing itself to begin an operation, after initialization ends; or
    * is actually executing the operation; or
    * is executing cleanup code after the operation, including the delegate message
        -worker:didEndWorkingWithFinalStatus:canceled:.
    If not in any of these situations, -isRunning returns NO.

    The values of some of the methods of a worker object (notably -progress and -cancelWasInvoked)
    are only meaningful while the worker is running.
*/
- (BOOL) isRunning;

@end

@interface General/NSObject (L0WorkerDelegate)

/*  -worker:didUpdateProgress:
    Sent whenever a new progress value becomes available.
*/
- (void) worker:(L0Worker*) worker didUpdateProgress:(float) progress;

/*  -worker:didEndWorkingWithFinalStatus:canceled:
    Sent whenever the operation of a worker ends. If the worker executed
    its operation successfully, err will equal nil; otherwise, it will be
    whatever error was returned by the worker. The canceled parameter will
    be true if -cancel was called on the worker at least once since the beginning
    of the most recent operation.
*/
- (void) worker:(L0Worker*) worker didEndWorkingWithFinalStatus:(General/NSError*) err canceled:(BOOL) canceled;

@end



----

L0Worker.m

    

//  Created by Emanuele on 06/09/05.
//  Copyright 2005 Emanuele Vulcano. Permission granted to everyone to use or modify this
//  code for any purpose.

#import "L0Worker.h"


@implementation L0Worker

- (id) init {
    if (self = [super init]) {
        // inits progress to indefinite; also sets isSynchronous = YES
        // as the default state.
        progress = kL0WorkerIndefiniteProgress;
        isSynchronous = YES;
        
        // NOTE ABOUT objectLock:
        // Although [objectLock lock] ... [objectLock unlock] blocks are everywhere
        // in this code, objectLock is always nil unless we're a. running and b.
        // running asynchronously, which means these two lines above are no-ops
        // most of the time.
    }
    
    return self;
}

- (void) run {
    // first, sets some flags...
    [objectLock lock];
    isRunning = YES;
    cancelWasInvoked = NO;
    [objectLock unlock];
    
    // does its job.
    General/NSError* finalStatus = [self work];
    
    // in the end, if the delegate responds to the appropriate message,
    // call it and signal the end of the operation.
    if (delegate && [delegate respondsToSelector:@selector(worker:didEndWorkingWithFinalStatus:canceled:)]) {

        BOOL localCopyOfCancelWasInvoked = [self cancelWasInvoked];
        id theDelegate = [self delegate];
        if (!theDelegate) theDelegate = General/[NSNull null];
        
        [self performSelectorOnMainThread:@selector(L0pvt_sendDelegateEndMessageWitParameters:)
                               withObject:General/[NSArray arrayWithObjects:finalStatus, General/[NSNumber numberWithBool:localCopyOfCancelWasInvoked], theDelegate, nil]
                            waitUntilDone:isSynchronous];
    }
    
    // resets some flags.
    [objectLock lock];
    isRunning = NO;
    progress = kL0WorkerIndefiniteProgress;
    [objectLock unlock];
    
}

// to be overridden by subclasses
- (General/NSError*) work {
    return nil;
}


// invoked on the main thread via performSelectorOnMainThread:...
// to send the -worker:didEndWorkingWithFinalStatus:canceled: delegate message.
- (void) L0pvt_sendDelegateEndMessageWithParameters:(General/NSArray*) array {
    General/NSError* final = [array objectAtIndex:0];
    BOOL canceled = General/array objectAtIndex:1] boolValue];
    id theDelegate = [array objectAtIndex:2];
    
    if (![theDelegate isKindOfClass:[[[NSNull class]] && [theDelegate respondsToSelector:@selector(worker:didEndWorkingWithFinalStatus:canceled:)])
        [theDelegate worker:self didEndWorkingWithFinalStatus:final canceled:canceled];
}

// Simply calls L0pvt_startWorking: on a separate thread...
- (void) detachWorkerThread {
    General/[NSThread detachNewThreadSelector:@selector(L0pvt_startWorking:) toTarget:self withObject:nil];
}

// ...sets up an autorelease pool and the object lock (making all accessors
// thread-safe) and then calls -run.
- (void) L0pvt_startWorking:(id) ignored {
    General/NSAutoreleasePool* pool = General/[NSAutoreleasePool new];
    objectLock = General/[[NSLock alloc] init];
    isSynchronous = NO; // this prevents blocking when delegate messages are sent.
    
    // let's run on this new, exciting thread!
    [self run];
    
    // releases the lock, cleans up.
    [objectLock release];
    objectLock = nil;
    isSynchronous = YES;
    [pool release];
}

/* * * * * * * * * * * * * * * * * */

- (BOOL) cancelWasInvoked {
    [objectLock lock];
    BOOL localCopyOfCancelWasInvoked = cancelWasInvoked;
    [objectLock unlock];
    
    return localCopyOfCancelWasInvoked;
}

- (void) cancel {
    [objectLock lock];
    cancelWasInvoked = YES;
    [objectLock unlock];
}

/* * * * * * * * * * * * * * * * * */

// both of these methods are thread-safe -- they can even
// change the delegate correctly while -work is running!
- (id) delegate {
    [objectLock lock];
    id localRefToDelegate = delegate;
    [objectLock unlock];
    
    return localRefToDelegate;
}

- (void) setDelegate:(id) newDelegate {
    [objectLock lock];
    delegate = newDelegate;
    [objectLock unlock];
}

/* * * * * * * * * * * * * * * * * */

- (float) progress {
    [objectLock lock];
    float localCopyOfProgress = progress;
    [objectLock unlock];
    
    return localCopyOfProgress;    
}

// throws an exception if newProgress isn't what we need it to be
// (newP != kL0WorkerIndefiniteProgress) || (newP <= 0.0f) || (newP >= 1.0f)
- (void) setProgress:(float) newProgress {
    General/NSAssert((newProgress != kL0WorkerIndefiniteProgress) || (newProgress < 0.0f) || (newProgress > 1.0f),
             @"The progress value for -[L0Worker setProgress:] must be either "
             @"kL0WorkerIndefiniteProgress or between 0.0 and 1.0!");
    
    [objectLock lock];
    progress = newProgress;
    [objectLock unlock];
    
    // sends the delegate a message
    [self performSelectorOnMainThread:@selector(L0pvt_sendDelegateProgressUpdateMessage:) withObject:General/[NSNumber numberWithFloat:newProgress] waitUntilDone:isSynchronous];
}

- (void) L0pvt_sendDelegateProgressUpdateMessage:(General/NSNumber*) newProgress {
    id theDelegate = [self delegate]; // this locks/unlocks if needed
    if (theDelegate && [theDelegate respondsToSelector:@selector(worker:didUpdateProgress:)])
        [delegate worker:self didUpdateProgress:[newProgress floatValue]];
}

// YES if -run's flags are set, NO otherwise.
- (BOOL) isRunning {
    [objectLock lock];
    BOOL localCopyOfIsRunning = isRunning;
    [objectLock unlock];
    
    return localCopyOfIsRunning;
}

@end


