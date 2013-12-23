---
layout: page
title: AutonomousThreadsUsingDistributedObjectsConnectionsAndRunLoops
---



The "Anatomy of a Complex Thread Method" is well documented at Apple's web site and in many web and book examples (ThreadWorker and BookCocoaProgramming). All of the examples I have found so far do not address the need to create an autonomous worker object that can be called upon to exit and to be released from memory after all ports have been removed and all thread cleanup has been successfully accomplished. After using Apple's example on how to form connections between threads using DO and an NSRunLoop and using the exit strategy implemented in the ThreadWorker example, I was having problems getting the thread to exit completely. Anyone familiar with the basic design of setting up communcation between threads using an NSConnection should be able to follow the source below: 

*note - the example code below contains a modified cleanup and exit sequence compared to the exit strategy presented in ThreadWorker (the details of a new sequence is presented a couple of sections below).  ThreadWorker uses a state variable to stop the run loop, but after reading the documentation for CFRunLoop a cleaner implementation has been found (see http://iharder.sourceforge.net/macosx/threadworker for the original exit strategy). The discussion in the paragraph following the sample code below will talk about problems that can be encountered when the receive port is not removed from the worker thread's run loop. ThreadWorker does not remove the receive port, but the example below does. Hope this does not cause too much confusion. --zootbobbalu*

    
- (void)connectWithPorts:(NSArray *)portArray
{
    pool = [[NSAutoreleasePool alloc] init];
    serverConnection = [NSConnection
                    connectionWithReceivePort:[portArray objectAtIndex:0]
                    sendPort:[portArray objectAtIndex:1]];
    NSMutableDictionary *reply = [NSMutableDictionary dictionaryWithObjectsAndKeys:
                                                @"addServer:", @"action",
                                                self, @"sender", nil];
    // client must implement addServer: and handleReply: methods
    port1 = [portArray objectAtIndex:0];
    port2 = [portArray objectAtIndex:1];
    clientConnection = [portArray objectAtIndex:2];
    
    [(id)[serverConnection rootProxy] performSelector:@selector(handleReply:) 
                                        withObject:reply];
    [[NSRunLoop currentRunLoop] run];
    
    [serverConnection invalidate];
    [clientConnection invalidate];
    [port1 invalidate];
    [port2 invalidate];
    [pool release];
    NSLog(@"WorkerObject: exit");

    [NSThread exit];
    return;
}

- (void)exit:(NSMutableDictionary *)message {
    [[NSRunLoop currentRunLoop] removePort:port1 forMode:NSConnectionReplyMode];
    [[NSRunLoop currentRunLoop] removePort:port1 forMode:NSDefaultRunLoopMode];
    CFRunLoopStop([[NSRunLoop currentRunLoop] getCFRunLoop]);  
}



Anyone who has read the chapter on threads in "Cocoa Programming" by Scott Anguish will know that his example code implements all the features of an autonomous worker objects except the ability to exit the worker object's run loop and perform thread/port cleanup (he even goes as far to point out that a nice challenge would be to enhance his example so that it can exit the run loop and stop the thread through a message call). 

Apple's documentation also walks you through detaching a worker thread, creating ports/connections/proxies and setting up a dedicated autorelease pool and run loop to support a distributed object, but even Apple doesn't explain how to exit the run loop and clean up the ports, connections, thread and run loop so that all memory associated with the worker object is properly released.

Well this discussion is an attempt to document proper thread cleanup when ports (NSConnections) are used for thread communication. 

Like I was saying, Apple already has good documentation on setting up a worker object, but Apple does not document how the worker object should properly exit the run loop. I think some rules need to be defined for proper memory management of distant objects (proxies), but what should these rules be? Here's a list of issues that need to be addressed:

*the question are now answered later in this discussion*

*What are the ramifications of retaining a distant object related to a thread's ability to exit properly
*Should a document retain a worker object or should a persistant object dole out worker objects and act as a go between (a proxy to the proxy) so that a document is free from managing worker object cleanup
*Do the ports associated with the NSConnection have to be explicitly removed from the worker thread's run loop for the run loop to exit properly?
*What exactly is the correct sequence of events that need to take place to invalidate/release/exit everything?
    
    [serverConnection invalidate];
    [clientConnection invalidate];
    [port1 invalidate];
    [port2 invalidate];

*In general, what are possible reasons for a thread's inability to exit properly when an object is distributed across threads.


----

So here is my initial attempt to answer the questions above:

After reading up on CFRunLoop I found out that run loops are not created or need to be destroyed when dealing with threads 

http://developer.apple.com/documentation/CoreFoundation/Reference/CFRunLoopRef/ *There is exactly one run loop per thread. You neither create nor destroy a thread�s run loop.*

This clears one question I had. Now after reading up on CFRunLoop I found the function to stop a run loop     CFRunLoopStop(CFRunLoopRef rl). This function does require that all ports the run loop is monitoring are removed first. The nice thing about     CFRunLoopStop is that you don't have to poll a state variable to exit the worker thread's run loop. Once the worker thread has exited it's run loop you still have to do some additional thread cleanup. The following is a sequence that is working for me in a document based appllication.


*Create a method for the single purpose of asking the server to exit (see     - (void)exit:(NSMutableDictionary *)message; in the example code at the top of this discussion)

*remove the receive port (    port1) from the run loop for the mode:    NSConnectionReplyMode
*remove the receive port (    port1) from the run loop for the mode:    NSDefaultRunLoopMode
*stop the worker thread's run loop -     CFRunLoopStop([[NSRunLoop currentRunLoop] getCFRunLoop]);

*after the line that starts the run loop (    [[NSRunLoop currentRunLoop] run];) do the final thread cleanup (here is where the main server method     connectWithPorts: continues after the run loop stops)

*invalidate the server connection:     [serverConnection invalidate];
*invalidate the client connection:     [clientConnection invalidate];
*invalidate the worker thread's receive port:     [port1 invalidate];
*invalidate the worker thread's send port:     [port2 invalidate];



Now the only issue to address is how a document based application should handle worker objects if each document is creating a dedicated worker object. After testing the cleanup procedure above, I noticed a problem if the document is closed before the server has time to handshake with the client (e.g. when the document is the client). You are going to need a clear strategy for exiting the worker thread if the client hasn't received a proxy for the server object before the client object deallocates. This situation can easily be created when you close a document window immediately after the window has been opened. A quick solution to this problem could be to do the following. Hopefully you get the idea.

    
@class WorkerObject;

@interface MyDocument : NSDocument {
    WorkerObject *workerObject;
}
@end


    
#import "MyDocument.h"
#import "WorkerObject.h"

...

- (void)windowControllerDidLoadNib:(NSWindowController *) aController
{
    [super windowControllerDidLoadNib:aController];
    [WorkerObject detachWorkerForClient:self];
}

- (void)handleReply:(NSMutableDictionary *)message {
    id sender = [message objectForKey:@"sender"];
    if ([sender isKindOfClass:[WorkerObject class]]) {
        workerObject = sender;
        [workerObject retain];
    }
}

- (void)someNotificationThatInformsTheDocumentThatTheUserWishesToCloseTheDocument:(NSNotification *)note {
    if (!workerObject) {
        [self performSelector:@selector(cleanupAfterWorkerObject) withObject:nil afterDelay:1.0f];
        return;
    }
}

- (void)cleanupAfterWorkerObject {
    if (workerObject) {
        [workerObject exit:nil];
        [workerObject release];
        workerObject = nil;
        [self dealloc];
     }
     else [self performSelector:@selector(cleanupAfterWorkerObject) withObject:nil afterDelay:1.0f];
}

...



    someNotificationThatInformsTheDocumentThatTheUserWishesToCloseTheDocument might seem like a silly method to implement, but the main reason I am writing discussion is because I was having a memory leak due to document objects not being deallocated when the document windows were being closed. Subviews in the document window were being removed from the window's content view but the document itself was not being released. I tracked this memory leak down to the worker object, and so my journey began. 

So now how should I answer the original questions I posted?


*What are the ramifications of retaining a distant object related to a thread's ability to exit properly

*I appears that a client can retain the server proxy safely after the server object has been asked to exit, but I assume it is not safe to send messages to a proxy with a dead connection (plus why would you do this). If your client object retained the server proxy after the initial server/client handshake, you should release the server proxy after asking the server to exit*

    
    [workerObject exit:nil];
    [workerObject release];
    [workerObject = nil;


*Should a document retain a worker object or should a persistant object dole out worker objects and act as a go between (a proxy to the proxy) so that a document is free from managing worker object cleanup

*I seems to me now that a document can safely manage the creation and destruction of a worker thread if the proper cleanup is done (see cleanup sequence above)*
*Do the ports associated with the NSConnection have to be explicitly removed from the worker thread's run loop for the run loop to exit properly?

*YES, the cleanup sequence above does not release the worker object or exit the thread properly if the receiving port in the worker thread is not removed from the run loop for the modes described above*

*What exactly is the correct sequence of events that need to take place to invalidate/release/exit everything?
    
    [serverConnection invalidate];
    [clientConnection invalidate];
    [port1 invalidate];
    [port2 invalidate];


*Not sure about this one. This sequence does work, so if anyone finds that this sequence doesn't work please post here*
*In general, what are possible reasons for a thread's inability to exit properly when an object is distributed across threads?

*I'll leave this one open*



----

You don't address how to clean up OS-added input sources. This is why conventional wisdom says that CFRunLoopStop is a bad idea.

