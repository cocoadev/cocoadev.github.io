---
layout: page
title: SmallSocketsOnConnection
---

Hi all,
Is there any way using smallsockets to do something like this:
    
if ([socket accecptConnection]) {
NSLog(@"Someone is trying to connect");
}

meaning... if someone tries to connect then do something...

----

The BSD way to solve this problem is to use the select(2) system call. Typically you'd do something like this (I don't do this often, so I might make a mistake or two, but this is the idea):

    
int sock;        // sock is our file descriptor
fd_set readfds; // readfds 

// begin listening
if (listen(sock, 5)) {
    return -1;
}

// wait for a connection
FD_SET(sock, &readfds);
if (select(sock+1, readfds, NULL, NULL, NULL)) {
    // perhaps we got a signal?
    return -1;
}

// accept the connection
if (accept(sock, &addr, &addrlen)) {
    return -1;
}


If I were designing a socket library, I'd use this kind of mechanism to build the following delegate API:

    
- (void)socketWillAcceptConnection:(Socket*)socket;
- (BOOL)socketShouldAcceptConnection:(Socket *)socket;
- (void)socketDidAcceptConnection:(Socket *)socket;


Alas, SmallSockets doesn't support delegates. Perhaps someone might file an enhancement request on SmallSockets' sourceforge page.

Poking through the SmallSockets code, however, there is a function that might do what you want: -(BOOL)isReadable. You might try the following:

    
[socket listenOnPort:port];

if ([socket isReadable]) {
NSLog(@"Someone is trying to connect");
    [socket accecptConnection];
    yada yada...
}


Oops... it looks like SmallSockets is too smart for its own good. You'll want to comment out the following lines in SmallSockets' isReadable method:

    
    /* MDT smallsockets, don't be a jerk!
    if ( !connected )
        [NSException raise:SOCKET_EX_NOT_CONNECTED 
                        format:SOCKET_EX_NOT_CONNECTED];
     */


(You might as well change isWritable too while you're in there.)

Since SmallSockets is supposed to be a wrapper on top of sockets, and you can call select on unconnected sockets, this is a bug in SmallSockets. I can see how someone might argue against making this change, but I think that's a pretty weak position: what good is a socket wrapper that doesn't allow socket semantics? But I digress ... Perhaps someone might file a bug on SmallSockets' sourceforge page.

So, give all that a try.

-- MikeTrent

----

I figured it all out people... I'm now using another thread using NSThread

----

That can be a good approach if your code is ThreadSafe. But it's not the only solution, and it's not appropriate for all projects.

-- MikeTrent

----

Another solution would be to use the CoreFoundation CFSocket API. This is a C interface but if there is interest I'll release the OO wrapper I've been working on. CFSocket uses the current RunLoop. This allows you to do asynchronous socket operations (like waiting for someone to connect) without using multiple threads. The CFStream API is also very useful. I have no idea why Apple hasn't exposed this functionality in the FoundationKit but it *is* in CoreFoundation and it *will* work in a Cocoa application.

-- PeterMonty

----

Hmmm... it looks like someone has already done an OO wrapper for CFSocket: NetSocket.

-- PeterMonty

