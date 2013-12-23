---
layout: page
title: SetProtocolForProxyCrashes
---

Hi, I was wondering if someone could help me. My app has several classes, and calls the other classes through a protocol and a proxy that uses that protocol. The problem is, however, that my app crashes with a EXC_BAD_ACCESS message on the SIGSEGV signal, when I try to setProxyForProtocol.

    - (IBAction)daemonStart:(id)sender
{
    daemonProcess = [[NSTask alloc] init];
    [daemonProcess setLaunchPath:@"~/Library/Application Support/mlPanther/mlnet"];
    [daemonProcess launch];
    [statusLine setStringValue:@"Connecting to file-sharing networks..."];
    [startButton setEnabled:NO];
    [stopButton setEnabled:YES];
    id ipcProxy;
    [ipcProxy setProtocolForProxy:@protocol(ipcStuff)];
    NSString *connectionReply = [ipcProxy startIPC];
    NSRunAlertPanel(@"Reply",connectionReply,@"OK",nil,nil); //for debugging purposes
    id rawIOProxy;
    [rawIOProxy setProtocolForProxy:@protocol(RawIO)];
    [rawIOProxy appendToConversation:connectionReply];
}

What am I doing wrong?

-- AaronRussell

----

You declare ipcProxy (which will have a random value taken from whatever data is on the call stack), and then you send it a message.  The runtime doesn't know you've given it an unintialzied varible, so it crashes. ++MarkDalrymple

----

So if I changed     id ipcProxy; to     id ipcProxy = nil; would that fix it?

-- AaronRussell

----

It would fix the crash, but then you'd be sending a message to a nil object, which is a no-op.  You'll need to do an alloc/init of the proper class (whatever it is that's taking the startIPC method), or use whatever method that class provides to get ahold of the IPC proxy.  It doesn't look like a standard Cocoa class, so I don't know what to suggest beyond that.  ++MarkDalrymple

----

The IPC class is a standard Cocoa class, but I can't figure out how to call it properly (I don't want to call it as     [IPC startIPC]; because that'd invoke     +(NSString *)startIPC instead of     -(NSString *)startIPC).

-- AaronRussell

----

Is it really a standard Cocoa class?  I don't see any "startIPC" in the cocoa header files (in fact, I can't find it in any *.h file in /System/Library/Frameworks).  Which makes me think it's one of  your classes, or something you're getting from another libbrary.  In any case, you need to figure out the class of this IPC object, and either create one of  your own, or get one from somewhere else. ++MarkDalrymple

----

Sorry, my brain was asleep when I said that. It's not a standard Cocoa class, no. IPC is a custom class and ipcStuff is the protocol for it. (Sorry, I thought you meant "standard Cocoa class" as opposed to a C/C++ one).

Here's my IPC.h file:
    #import <Foundation/Foundation.h>


@interface IPC : NSObject
{
}
@end

@protocol ipcStuff
- (NSString *)startIPC;
@end

-- AaronRussell

----

You might want to familiarize yourself a little more with Obj-C before wrapping your own classes. Here is the basic paradigm:

A class object represents a mold. From that mold you can create instances (or objects that have the functionality defined withing the class). Instances are able to access instance variables, which can be defined between the curly brackets at the top of the class definition, and are able to use both class methods and the instance methods you define or that have been defined. Class methods are prefixed with a +, and instance methods are prefixed with a -.

Protocols (in obj-c) define standardized methods for classes to implement, even though that goes against one of the fundamental principles of a procol in general (that some linear property be defined such that the operations are executed in order).

What exactly are you trying to accomplish. I might be able to help more if I had a broader idea.

BTW: instances are created in the following manner:
    
id anInstance = [[InstanceClass alloc] init];


where -init can be replaced with any initializer for the object (for instance initWithString: for NSString).

This is what you need to do to fix your runtime error (create an instance of your class) --EliotSimcoe

----

I've written a class that handles data transmission to/from the server process. I want to be able to call it from any other class as an instance method. But I can only figure out how to call it as a class method. In your example, what would I put in place of     InstanceClass to be able to call the instance method     startIPC from the class     IPC?

----

You haven't actually implemented the method, so there is no way for you to call it. Check out http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/index.html it will answer all of your questions. Read it thoroughly --EliotSimcoe

----

I figured it out. I was missing     @class IPC; from my calling class's header file. D'oh. Thanks for your help Mark and Eliot!

--AaronRussell

