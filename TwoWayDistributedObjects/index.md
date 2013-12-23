---
layout: page
title: TwoWayDistributedObjects
---

Just a quick question to anyone who might know the answer.  Is it possible for General/NSConnection clients to vend objects to servers so that the a client could access a server's proxy class and a server in return could access a connected client's class as well?  Right now my code works well one way.

My server code looks like this:

    
socketPort = General/[[NSSocketPort alloc] initWithTCPPort:TCP_PORT];

connection = General/[[NSConnection alloc] initWithReceivePort:socketPort sendPort:nil];
[connection setRootObject:self];
if ([connection registerName:[newGamePlayer1 stringValue]] == NO)
{
	General/NSRunAlertPanel(@"Connection Error!", @"Could not register connection", @"OK", nil, nil);
	return;
}
[connection setDelegate:self];

// Configure and publish the Rendezvous service
service = General/[[NSNetService alloc] initWithDomain:@""
		    type:@"_service._tcp."
		    name:[newGamePlayer1 stringValue]
		    port:TCP_PORT];
[service setDelegate:self];
[service publish];


And my client code looks like this:

    
hostService = [discoveredServices objectAtIndex:[gameList selectedRow]];

serverPort = General/[[NSSocketPort alloc]
	initRemoteWithProtocolFamily:AF_INET
	socketType:SOCK_STREAM
	protocol:0
	address:General/hostService addresses] objectAtIndex:0;

connection = General/[NSConnection connectionWithReceivePort:nil sendPort:serverPort];
server = (Mainframe *)[connection rootProxy];


----

Once you have a DO connection set up, you can treat the objects on the other side of the connection just like they were local (with very few exceptions). So, normally you might send something like     [serverObject setRemoteObject:self], and     serverObject will store that object in a list somewhere. The same thing will work with DO. You will have to be careful, if the two ends are in different processes and especially if they're on different machines, that the connection can die unexpectedly, and sending messages to a remote object will throw exceptions.

----

I thought I had tried that, but I went back to my code just to be sure.  I thought it might be because I passed the remote object as (id), but that wasn't it.  Add this to my client code:

    
if (server == nil)
{
	General/NSLog(@"couldn't connect with server\n");
}
else
{
	[server addRemoteObject:self];
	[server retain];
}


On the server I add the function:

    

- (void)addRemoteObject:(Communicate *)remote;
{
	[self setRemoteObject:remote];
}

// And in another function on the server I try to call a client function

[[self remoteObject] startLocalTurn];



When the "startLocalTurn" method is called, I get a EXC_BAD_ACCESS error on the server.

----

Does     [self setRemoteObject:remote] retain its argument? This is one of the exceptions to the "treat everything as if it were local" rule. Memory management is strictly *local*. If you don't retain an object that comes from across a DO connection, it will crash even though the real object on the other side is still around.

The way to think of it is like this: object lifetimes aren't infinite, and you also want to reduce traffic across the link as much as possible. Therefore, messages like retain and release aren't actually sent to the remote object. As long as the proxy is around, it retains the remote object. Once the proxy is deallocated, it releases the remote object. This keeps you from having to wait for a roundtrip every time you send a retain, and allows the proxy to know when it's no longer required. So you message the remote object, but you retain and release its local proxy. I hope that isn't too confusing. Most of the time you don't even have to deal with it, but it does come into play here if you're not retaining a proxy but still expecting it to stick around.

----

Thanks.  That worked.  I should have considered it since I also needed to retain the server object.
