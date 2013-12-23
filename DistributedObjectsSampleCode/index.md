---
layout: page
title: DistributedObjectsSampleCode
---



From John Hornkvist (MacOsxDev mailing list):

    

Here is code that I know to work:

// DOTest.h

#import <Foundation/Foundation.h>

@interface Client:NSObject
{
         id serverObject;
}

- (void) connect;
- (void)log: (NSString*)string;
@end

@interface Server:NSObject
{
         NSConnection* serverConnection;
}
- (void)log: (NSString*)string;
- (void)serve;
- (NSConnection*) createConnectionName:(NSString*)name;
@end

----

// DOTest.m

#import "DOTest.h"


@implementation Client
- (void) connect
{
   serverObject=[NSConnection
rootProxyForConnectionWithRegisteredName:@"MyServer" host: nil];
}
- (void)log: (NSString*)string
{
         [serverObject log: string];
}

@end


@implementation Server

- (void)log: (NSString*)string
{
         NSLog(string);
}

- (void)serve
{
         serverConnection=[self createConnectionName:@"MyServer"];
         [[NSRunLoop currentRunLoop] run];
}
- (NSConnection*) createConnectionName:(NSString*)name
{
   NSConnection* newConnection=[[NSConnection alloc] init];
   if ([newConnection registerName:name])
     {
       [newConnection setRootObject:self];
     }
   else
     {
       [newConnection release];
       newConnection=nil;
     }
   return newConnection;
}

@end

----

// server.m

#import "DOTest.h"

main()
{
         NSAutoreleasePool* pool=[[NSAutoreleasePool alloc] init];
         Server* server=Server alloc] init];

         [server serve];

         [pool release];
}

----

// client.m

#import "[[DOTest.h"

main()
{
         NSAutoreleasePool* pool=[[NSAutoreleasePool alloc] init];
         Client* client=Client alloc] init];

         [client connect];
         [client log:@"Hello, world!"];

         [pool release];
}

----

To test:
cc -c -o [[DOTest.o DOTest.m
cc -o client client.m DOTest.o -framework Foundation
cc -o server server.m DOTest.o -framework Foundation

And then launch client in one Terminal window and Server in another. The
"Hello, world!" will be sent from the client and logged in the servers
window.

I tested this a moment ago, and it works perfectly.

Regards,
John Hornkvist


----
----

From Lloyd Sargent

Canna Software

lloyd AT cannasoftware DOT com

This is a distilled version of the above. The idea is that we have a main thread and
 a child thread (I do not include code to create the child thread). The problem is that
we want the child thread to have access to the main thread's methods (for example if
we need to pass information to it).

What is really nice about this is that it is thread-safe and relatively easy to set up. The
downside is remembering that the connection object MUST BE RETAINED in the child thread.
You snooze, you lose (and then you crash).

Note that it ONLY sends messages in ONE direction (in this case from the child thread to
the main thread).

    

@interface MainThread:NSObject
{
    NSConnection *destConnection;
}
- (void) YourMethod;
- (void) amessage;
@end


@implementation MainThread

- (void) YourMethod
{
    //----- now create the connection
    destConnection = [[NSConnection alloc] init];    // create connection object
    if ([destConnection registerName:@"Any Name You Want"])
    {
        [destConnection setRootObject: self];
        NSLog(@"DestConnection Created!");
    }
    else
    {
        [destConnection release];
        destConnection = nil;
        NSLog(@"DestConnection didn't get created");
    }

    //----- start ChildThread up

     //----- more code
}

- (void) amessage
{
    NSLog("Message for you sir!");
}
@end


@interface ChildThread : NSObject
{
    id srcConnection;
    // note: it is better you static type this to MainThread (or whatever your parent thread
    //      is named. You will avoid warning messages.
}
- (void) ChildThread;
@end

@implementation
- (void) ChildThread
{
    // MUST have this or you will leak!
    NSAutoreleasePool *localPool = [[NSAutoreleasePool alloc] init];

    //----- set up the proxy object so that we can directly talk to the MainThread
    srcConnection = [[NSConnection
        rootProxyForConnectionWithRegisteredName:@"Any Name You Want"
        host: nil] retain];
    // remember to retain any object you get - esp. if you will be in
    // a loop! We don't want to constantly make/break connections!

    // now we use it as if it were a MainThread object!
    [srcConnection amessage];
    // however, note that the communication is only child to main
    // ALSO note that this will give a warning unless you static type
    // srcConnection.

    [srcConnection release];    // remember to release it
    [localPool release];
}
@end



----

Some more Sample Code form Apple's Chris Kane:

Weeks ago now, there was discussion of using DO over TCP, and I said 
I'd write up an example.  Well, things are very busy here, and I 
finally got around to doing that only a couple days ago.  The example 
assumes you don't need DO explained.  It requires Mac OS X Public Beta 
or later.

The example is very simple, just a random number server.  With 
USE_SOCKETS equal to 0, it uses Mach ports, otherwise TCP sockets 
(NSSocketPort).  Compile with the comment at the top, and make a copy 
of the binary, and name one 'client' and one 'server'.

Run the server on the local machine or a remote one.  If the client is 
started without any arguments, it tries to connect to the server on the 
machine the client is running on.  Give it the name of the remote host 
if the server is running on another machine.  Remote only works for TCP 
socket transport.

The code is written to illustrate the differences in choosing a 
transport with as few differences as possible.  See the use of 
USE_SOCKETS in the server() function to see its differences.  The 
client "figures out" which style the server is using by trying Mach 
ports, then TCP sockets.  (I'm not saying real programs should do that.)

Real-world example: The Mac OS X build system is a complex system for 
farming out project builds to build servers and managing dependencies 
and whatnot, and it has used DO over TCP for some time, using its own 
hand-rolled TCP transport.  So DO over TCP is possible.  That system 
isn't using NSSocketPort yet (it predates NSSocketPort), but we'll be 
switching it to NSSocketPort sometime after Mac OS X is released, which 
will eliminate several hundreds of lines of code.  I've given the 
engineers that will do that this same example to work from.


Chris Kane
Cocoa Frameworks, Apple, Inc.

    

// cc -Wall -g -framework Foundation -O -o server do_test.m; cp server client

#import <Foundation/Foundation.h>

#if !defined(USE_SOCKETS)
 	#define USE_SOCKETS 1
#endif

@protocol ServerProtocol
- (void)setRandomSeed:(unsigned int)s;
- (long)getRandom;
@end

@interface Server : NSObject <ServerProtocol>
@end

@implementation Server

- (void)setRandomSeed:(unsigned int)s {
    srandom(s);
}

- (long)getRandom {
    return random();
}
@end

#define SERVER_PORT 15550
#define SERVER_NAME @"TEST"

void server(int argc, const char *argv[]) {
     NSPort *receivePort = nil;
     NSConnection *conn;
     id serverObj;

 #if USE_SOCKETS
     receivePort = [[NSSocketPort alloc] initWithTCPPort:SERVER_PORT];
 #else
     // Mach ports being "anonymous" and need to be named later
     receivePort = [[NSMachPort alloc] init];
 #endif
     conn = [[NSConnection alloc] initWithReceivePort:receivePort 
 sendPort:nil];
     serverObj = Server alloc] init];
     [conn setRootObject:serverObj];
 #if USE_SOCKETS
     // registration done by allocating the [[NSSocketPort
     printf("server configured to use sockets\n");
 #else
     if (![conn registerName:SERVER_NAME]) {
 	printf("server: set name failed\n");
 	exit(1);
     }
     printf("server configured to use Mach ports\n");
 #endif

     [[NSRunLoop currentRunLoop] run];
 }

 void client(int argc, const char *argv[]) {
     NSPort *sendPort = nil;
     NSConnection *conn;
     id proxyObj;
     long result;
     NSString *hostName = nil;

     if (1 < argc) {
 	hostName = [NSString stringWithCString:argv[1]];
     }

     sendPort = [[NSMachBootstrapServer sharedInstance] 
 portForName:SERVER_NAME host:hostName];
     if (nil == sendPort) {
 	// This will succeed (if host exists), even when there is no server
 	// on the other end, since the connect() is done lazily (arguably wrong),
 	// when first message is sent.
 	sendPort = [[NSSocketPort alloc] initRemoteWithTCPPort:SERVER_PORT 
 host:hostName];
     }
     if (nil == sendPort) {
 	printf("client: could not look up server\n");
 	exit(1);
     }
     NS_DURING
 	conn = [[NSConnection alloc] initWithReceivePort:(NSPort*)
              sendPort class] port] sendPort:sendPort];
 	proxyObj = [conn rootProxy];
     NS_HANDLER
 	proxyObj = nil;
     NS_ENDHANDLER
     if (nil == proxyObj) {
 	printf("client: getting proxy failed\n");
 	exit(1);
     }
     [proxyObj setProtocolForProxy:@protocol([[ServerProtocol)];
     printf("client configured to use %s\n", ([sendPort class] == 
 [NSSocketPort self]) ? "sockets" : "Mach ports");

     result = [proxyObj getRandom];
     printf("random #: %ld\n", result);
     result = [proxyObj getRandom];
     printf("random #: %ld\n", result);

     printf("\nset seed\n");
     [proxyObj setRandomSeed:17];
     result = [proxyObj getRandom];
     printf("random #: %ld\n", result);
     result = [proxyObj getRandom];
     printf("random #: %ld\n", result);

     printf("\nset seed\n");
     [proxyObj setRandomSeed:17];
     result = [proxyObj getRandom];
     printf("random #: %ld\n", result);
     result = [proxyObj getRandom];
     printf("random #: %ld\n", result);
 }

 int main(int argc, const char *argv[]) {
     id pool = [[NSAutoreleasePool alloc] init];
     if (0 < argc && 0 == strcmp(argv[0] + strlen(argv[0]) - 6, "server")) {
 	server(argc, argv);
     } else {
 	client(argc, argv);
     }
     [pool release];
     exit(0);
 }

