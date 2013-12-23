---
layout: page
title: SocketCommunicationHelp
---

I have written an application for our company  that serves as an in/out board, production status indicator, and instant messanger.  It is a rather crude and simple application, but it works.  It was written in REALbasic and consists of a server app and client apps.  The communication is conducted via REALbasic sockets that just send character strings back and forth.

I would like to write a client app in Cocoa and I can't seem to find any help with sockets.  I suspect that NSSocketPort doesn't work quite the same way as REALbasic sockets but I'd like for the cocoa client app to be able to communicate with the existing server app - that is, be able to connect to the socket (by port and ip address) and read and write text strings over the socket.

Is this possible?

I'd really appreciate any help this community can offer.

Thanks,
Phil

----

http://www.macdevcenter.com/pub/a/mac/2002/12/26/cocoa.html
--zootbobblu
----
Cocoa sockets don't work the same?  I tried the code at the link above but my test chokes at the "inet_pton()" function.  Also, how would I be notified of new data in the read buffer?

I would have thought that an NSSocketPort (or some other Cocoa structure) would have some delegate to handle incomming data.

Sorry to sound so new at this, but I'm so... , well, new at this.

Thanks,
Phil

----

If you're really green at socket programming then I suggest you bone up on the subject of Unix interprocess communication. W. Richard Stevens wrote a great two volume set on the subject ("Unix: Network Programming - Interprocess Communication" vol 1&2). You are playing with fire if you don't know what you're doing with sockets, because if you start openning up ports on machines connected to the internet you are essentially bypassing security messures put in place by the default Mac OS X install (don't assuming a hardware firewall is the only defense needed to keep the bad people out, many times the bad people hack from the inside). Only play with sockets after security issues are addressed (talk with your LAN administrator first!!). 

Anyways, what type of systems are you trying to link together. If they are all Mac OS X machines, you can use NSConnections with NSProxies to send messages between cocoa objects running on different Macs.

Are you trying to mix Cocoa clients with RealBasic clients?

Is the server writen in Cocoa or RealBasic?

Timing issues are not that simple. You have to know what your comminication layers are first. I'm not familiar with RealBasic sockets, do you know what protocols are being used?

--zootbobbalu
----
First of all:  thanks for the responses.

As I said, the apps are very simple.  The server and all of the clients are written in REALbasic.  The server is running on a WinNT machine and 3 clients are running under OS X  and 20 are on OS 9 machines.  All communication is done using port 2001 and the server is running on a private ip.

Here's how it works:  On startup, the server listens.  On startup, the client establishes a connection with the server on port 2001. When an employee changes his/her status (in/out) and any other details, the client writes an 'update string' to the server.  The server parses the string, if necessary, and broadcasts the string over all of it's sockets  to pass the update along to the clients.  That's essentially it.  Most of the time the clients just wait for an 'update string' to arrive at the socket.  Oh, there is instant messaging, a bulletin board and various other alerts, but that's just a matter of parsing the 'update string' and manipulating the UI.

I do have REALBasic OS X clients, but this is a very simple app and I wanted to use this as an opportunity to learn Cocoa.  Appart from my lack of understanding of objC/cocoa sockets I think I can handle the rest.  Sockets in RB are very simplistic (as is much of RB).

Anyway, thanks again for all of your help,

Phil
----

Here's some online docs on BSD sockets:

http://developer.apple.com/techpubs/macosx/Essentials/SystemOverview/InverEnvironissues/

http://world.std.com/~jimf/papers/sockets/sockets.html


You can use an NSFileHandle for stream type sockets (like I was saying before, timing issues are not that simple, you really should know what your communication layers are at the start or else debugging becomes a real nightmare). 

http://developer.apple.com/techpubs/macosx/Cocoa/TasksAndConcepts/ProgrammingTopics/LowLevelFileMgmt/Concepts/FileHandle.html

If you're dealing with datagrams or packet style messaging you are going to have to learn some low level concepts because tricky system level calls are involved. 

If you know RealBasic is using stream type sockets, then NSFileHandle is your best bet. Were you successful at getting a socket id? If you did, you can use the following method to initialize an NSFileHandle for the socket.

[myFileHandle initWithFileDescriptor:(int)socketID]

you can ask "myFileHandle" to notify you of incoming data by telling it to read in background.

[myFileHandle readInBackgroundAndNotify]

then you have to register an observer object to receive an NSNotification with the name   NSFileHandleReadCompletionNotification. 

    
[NSNotificationCenter addObserver:observer 
                                  selector:@selector(yourMethod:) 
                                  name:  NSFileHandleReadCompletionNotification 
                                  object:nil]


"yourMethod:" should look something like this:

    
-(void)yourMethod:(NSNotification *)notification {
   id notificationObject=[notification object];
   if (notificationObject==myFileHandle) {

       // your code

    }
    if (notificationObject==someOtherFileHandle) {

        // code for some other file handle
    }
}


you have to keep asking "myFileHandle" to read in background every time a request/reply comes in.



--zootbobbalu
----
Again, thanks for your help.

It is clear that the handling of sockets via cocoa/objC/bsd is not NEARLY as simple as it is with RB.  In RB it's just a matter of instantiating a socket, setting port and address properties, and reading and writing strings to it.  It looks like I'm going to have to get my hands dirty with these sockets.

Clearly I have quite a bit of studying to do before I can dive into this project.

Thanks again for pointing me in the right direction(s)!

-Phil
----
Just an update (and for those in my predicament):

blackholemedia's (blackholemedia.com) NetSocket class seem to do what I want.  That is to say, it functions much like the sockets I'm accustomed to with RB.

Thanks again to zootbobbalu,
Phil

----

That's cool! just bookmarked blackholemedia.com

--zootbobbalu

----

Also check out AsyncSocket. -- DustinVoss

I second AsyncSocket over NetSocket - you have more configuration options - for instance you can declare packet sizes and how many packets you want to queue up so as not to flood the kernel etc.  --EcumeDesJours

