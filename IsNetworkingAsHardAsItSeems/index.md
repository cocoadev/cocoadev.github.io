---
layout: page
title: IsNetworkingAsHardAsItSeems
---

My question is as follows.  Is the only way to set up a simple client/server connection to use bsd sockets, about which whole books could be written, or is there something simpler?  Is there anything which just lets me start as a server in one line, or connect to one with one line?  Is there any easy way to send bytes, and know when there are bytes available?

-Charlie

----

Take a gander at NetSocket.  I think you'll be pleasantly surprised. -- Bo

Other socket classes: AsyncSocket, SmallSockets, DizSocket?, OmniNetworking. The best way to do sockets simply is to use one of these classes. I just created a SocketClasses page. -- DustinVoss

----

What about NSConnection?  Would this help me at all?  I'm just having trouble figuring out how it's done... say I want to send an NSString @"1" if it's rock, @"2" if it's paper, @"3" if it's scissors, or even using an integer in a rock/paper/scissors network game.  From what I read in the stepwise article, it looks as though it reads it all into one mutable string, or one nsdata.  Can someone shed some light on how I should implement something as simple as 1, 2, or 3, in laymen's terms?

-Charlie

----

For the example you describe, an NSConnection might be an easier way to go.  PDOs (Portable Distributed Objects) allows you to create an object in a different thread (or a different computer across a network) and call methods remotely from a client.  I have not used them too much but there is an article on CocoaDevCentral ( http://cocoadevcentral.com/articles/000062.php ) that gives a nice overview along with a step-by-step explanation of a sample project.

The basic concept is that you have the server create an NSConnection and "vend" an object.  The client will need to create an NSConnection, connect to the server and create a link to the vended remote object.  You can then call any method that the remote object supports and it will execute on the remote machine.  I have done this and called methods that send in NSStrings to the remote object and have gotten return values(bool, int) to my client.  I'm not sure it would return complex data structures like objects or structs.
    
(on the server)
(.h file)
    NSConnection	*connection;
    NSSocketPort  	*port;

(.m file - awakeFromNib maybe?)
    port = [[NSSocketPort alloc] initWithTCPPort:12345];
    connection = [[NSConnection alloc] initWithReceivePort:port sendPort:nil];
    [connection setRootObject:self];

(on the client)
(.h file)
    id			remoteObject;
    NSConnection	*connection;
    NSSocketPort  	*port;

(.m file - awakeFromNib maybe?)
    port = [[NSSocketPort alloc] initRemoteWithTCPPort:12345 host:@"192.168.0.200" ];
    connection = [[NSConnection connectionWithReceivePort:nil sendPort:port] retain];
    remoteObject = connection rootProxy] retain];

(then the client can call)
    [remoteObject remoteMethod:@"Rock"];

-- [[DaveW

----

NSConnection is strictly for DistributedObjects. DO can be used for networking, but in an Object Oriented fashion, not Internet-style. -- DustinVoss 

----
I got along with sockets real easy.. First I learned how to use BSD Sockets. So now I know that, but.. I later on found out that there were classes such as NetSocket and SmallSockets which doesn't require any knowledge of BSD Sockets at all.... Bugger =)  --AntonKiland

----
Is there somewhere a good tutorial about the memory management with distributed objects? When to retain and on which side, when to use in, out, bycopy or byref? That would be very helpful. At the moment I get strange memory problems at the receiving site of my application (passing an NSArray).

I need some more explanation concerning the memory management within the topic of network programming. If you send a pointer over a network connection, is the variable retained or copied or what? What is the best way to pass complete objects, bycopy? Hmm, some many questions...

--ThomasSempf

----
You can't send a pointer over a network connection. Pointers access memory on the local computer with the pointer reference. Reading that doc about memory management might be helpful; it's crucial that you understand the difference between a pointer and actual data. (Or, alternatively, ignore pointers and not have to learn the difference.)

A short answer might be: You're sending data, not a pointer. There is no useful way to usefully [sic] send a pointer over a network. A pointer without the data to which it refers is useless; the memory address you send would not point to the real data on the other end. 

--JoshuaMarker

----

Sure, normally that is right, but with distributed object it is possible to do that. If you look at the developer infos at the apple website:

http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/4objc_runtime_overview/chapter_4_section_5.html

you will see that it is possible to send pointers. They refer to the memory of the remote application, but they are copied at some circumstance, too. So, there is my question: what are the usual memory management rules for distributed objects?

--ThomasSempf

*Most of your questions are answered right there in the docs. The section on remote messaging states pretty explicitly when you should use the different qualifiers, in, out, inout, bycopy, etc. As far as retaining goes, I think you*re making things more complicated than they need to be. No matter which side of the communication you're on, retain something if you need it to stick around, and release it when you're done. If you passed the object by reference, then the remote side is really talking to a proxy, and you can count on the proxy to do the right thing when you retain and release it. If you passed the object by copy, then the remote side has its own copy and can retain/release it just as you would any other local object.*
----

OK, I have some more questions, hopefully there are some answers out there. How to release NSConnection gracefully on client and server side?
I try something like:

    
	[serverObj release];
	serverObj = nil;
	[conn invalidate];
	[conn release];
	conn = nil;
	[receivePort invalidate];
	[receivePort release];
	receivePort = nil;	


But still the port on the server side is blocked, after I restarted the server. Are there any notifications a server or client gets when one side is stopping the connection?

--ThomasSempf

----

Theres     NSConnectionDidDieNotification but:

*One limitation of NSConnectionDidDieNotification, however, is that an NSConnection object attached to a remote NSSocketPort object (the send port) cannot detect when the remote port becomes invalid. This is true even if the remote port is on the same machine. Therefore, an NSConnection object cannot post an NSConnectionDidDieNotification when the connection is lost. Instead, you must detect the timeout error when the next message is sent and invalidate the NSSocketPort object yourself.*

[http://developer.apple.com/documentation/Cocoa/Conceptual/DistrObjects/Tasks/errors.html]

----

For good example code of networking, I found Apple's "CocoaEcho" sample code to be very nifty and useful. The server console app runs on one machine, and the same machine or another machine on the local subnet runs the client, and it sends simple text strings between them, to show an example of what it can do. Try that and see if it helps. -- JasonTerhorst

And after I did a quick search, I've found you a link: http://developer.apple.com/samplecode/CocoaEcho/CocoaEcho.html
It's very cool, since it covers IPv4 and IPv6, Bonjour (very basic), and related topics...

----

Is it possible to run PDO on non-OS X platforms, using GNUStep and gcc for example?  I'm writing a server, and PDO looks like a nifty alternative to CORBA and its cousins.

-KevinPrichard

