---
layout: page
title: DistributedObjectsForInterThreadCommunication
---

Example code for server and client threads communication using DistributedObjects. 

The code is based on Apple article (http://developer.apple.com/documentation/Cocoa/Conceptual/Multithreading/articles/CocoaDOComm.html) and DistributedObjectsForInterThreadCommsCrash.

Notes:


* The DO voodoo is hidden in Server class, the client need only one method for setting the server
* Does not leak mach ports like Apple example code


Issues:


* It would be nice to hide the DO voodoo completely and create the worker thread with [Server server]
* It would be nice to create the server thread synchronically, blocking until it is created.



    
//// Server.h

/* 

Creating a server thread
------------------------
Client call [Server connectionToServerThreadForClient:self] to create a
server thread and get back a connection to the thread. When the thread
is ready, it will call the client setServer: method. After the client
set the server, it can communicate with the server over DO.

Destroying a server thread
--------------------------
Client call [server terminate], then release the server as usual.

*/

@protocol Server

// Terminate the server thread
- (oneway void)terminate;

// Add other methods that servers must have

@end


@protocol Client

// Called from the server when its ready
- (void)setServer:(in id)server;

// Add other methods that clients must have

@end


@interface Server : NSObject <Server>
{
    BOOL running;
}

// Create new thread and get back a connection the thread
+ (NSConnection *)connectionToServerThreadForClient:(id <Client>)client

// Private method used only by the Server class to connect to a new created thread
+ (void)connectWithPorts:(NSArray *)portArray;

- (BOOL)isRunning;

@end

//// Server.m

@implementation Server

+ (NSConnection *)connectionToServerThreadForClient:(id <Client>)client
{
    NSPort *port1 = [NSPort port];
    NSPort *port2 = [NSPort port];
    
    NSConnection *connection = [NSConnection connectionWithReceivePort:port1
                                                              sendPort:port2];
    [connection setRootObject:client];
 
    // Ports switched here
    NSArray *portArray = [NSArray arrayWithObjects:port2, port1, nil];
 
    [NSThread detachNewThreadSelector:@selector(connectWithPorts:)
                             toTarget:[self class] 
                           withObject:portArray];
    return connection;
}


+ (void)connectWithPorts:(NSArray *)portArray
{
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
    Server *instance = self alloc] init];

    [[NSConnection *connection =
        [[NSConnection alloc] initWithReceivePort:[portArray objectAtIndex:0]
                                         sendPort:[portArray objectAtIndex:1]];
    [connection setRootObject:instance];

    // Connect with the client
    [(id <Client>)[connection rootProxy] setServer:instance];

    // Run until termination
    do {
        [[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode
                                 beforeDate:[NSDate distantFuture]];
    } while ([instance isRunning]);

    // If not invalidated, the ports leak and instance is never released
    connection receivePort] invalidate];
    [[connection sendPort] invalidate];

    [connection release];
    [instance release];
    [pool release];
}

- (void)terminate
{
    running = NO;
}

- (BOOL)isRunning
{
    return running;
}

@end



    
//// Client.h

#import Server.h // for Client protocol

@interface Client : [[NSObject <Client>
{
    id <Server> server;
    NSConnection *serverConnection;
}
@end


//// Client.m

@implementation Client

- (void)setServer:(id)anObject
{
    [anObject retain];
    [anObject setProtocolForProxy:@protocol(Server)];
    [server release];
    server = (id <Server>)anObject;

    // You may start to communicate with the server now
}

@end



--NirSoffer

