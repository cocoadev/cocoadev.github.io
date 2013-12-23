---
layout: page
title: SocketsAppleVsOthers
---

NSSocketPort and NSFileHandle work fine and are compact, buffered and easy to implement.  Why are there so many alternative socket/networking APIs?  what do the others (smallsockets etc) offer?

I use something like this for the server socket:

    
- (void)setupSocket
{
    socketPort = [[NSSocketPort alloc] initWithTCPPort:12341];
    listeningSocket = [[NSFileHandle alloc]
                         initWithFileDescriptor:[socketPort socket]];
}


and to read and write:

    
 myFileHandle = [[NSFileHandle alloc] initWithFileDescriptor:[socketPort socket]];


I can't imagine things getting easier than this...

----

Sockets are used for transparent communication between processes which may not be on the same computer. So you may wish to connect to your ISP's mail server, accept iChat connections from your co-worker, receive streamed audio where it's okay that some packets are lost etc. etc. -- none of this is really possible with the scheme you outline above.

Isn't NSSocketPort part of Distributed Objects?  Will you have DO overhead / confusion if the process on the other end doesn't do DO? (or if you then try a DO operation over this socket port)

----

I tried the above method, but couldn't get it to work. The NSSocketPort works fine using initRemoteWithTCPPort, but when I try to pass the socket to a filehandle, the file handle says that the socket number is invalid.
So I checked the socket number, and its some huge number, something like 0x3ff0000.

I'm trying to have this socket communicate with a regular ol' BSD socket. could that be causing the problem?
Any help would be appreciated.

----

Well, sure, NSSocketPort isn't designed for Internet-style stream communications. You can use NSFileHandle, and now NSInputStream and NSOutputStream, but the actual SocketClasses that we all wrote are still easier for Internet-style networking. AsyncSocket, for example, my class, is *designed* to handle packets, and offers send and receive queues and DNS lookup and other useful stuff right there.

