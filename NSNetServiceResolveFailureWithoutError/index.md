---
layout: page
title: NSNetServiceResolveFailureWithoutError
---

Hey

I have been playing around with Bonjour recently and have been trying to setup a little service with a server and a client.
Unfortunately, things do not always work quite as I would expect. 

In some cases both NSNetService's resolve and resolveWithTimeout (resolve I believe just calls resolveWithTimeout of 6 now) will fail without warning.
By without warning I mean they will call the delegate


-(void)netServiceWillResolve:(NSNetService *)sender


 but they will not afterwards call


- (void)netServiceDidResolveAddress:(NSNetService *)aNetService or - (void)netService:(NSNetService *)aNetService didNotResolve:(NSDictionary *)errorDict 


I have made every delegate I could find reference to and none of them get called sometimes when I am showing off the service. The one thing I have noticed that
is consistent is that if I start my NSNetServiceBroswer first and start looking before the server starts advertising its services, the resolve will always properly occur.
If I however start the client first, then right after start the NSNetServiceBrowser it will most often not resolve. It does however detect the service. It also will
resolve sometimes almost making me think there is a race condition. I thought propgation might be the issue so i left both on over night once and still nothing.
Both have firewalls that are off and I am connecting through a dumb hub.
Just to give you some snippets can anyone tell if I am doing anything insane?


Server Code -- Remember its just a snippet so just assume anything not defined is defined else where properly
     

	socketPort = [[NSSocketPort alloc] initWithTCPPort:0];
	serverConnection = [NSConnection connectionWithReceivePort:socketPort sendPort:nil];
	[serverConnection setRootObject:PDOObject];

	struct sockaddr *addr = (struct sockaddr *)socketPort address] bytes];
	int port = -1; 
        //Watch out for endianess :P
	if(addr->sa_family == AF_INET) {
		port = ntohs(((struct sockaddr_in *)addr)->sin_port);
	} else if(addr->sa_family == AF_INET6){
		port = ntohs(((struct sockaddr_in6 *)addr)->sin6_port);
	} else {
		[[NSLog(@"Socket Error: The socket supplied is not IPv4 or IPv6");
		return;
	}

	bonjourService = [[NSNetService alloc] initWithDomain:@"local" type:@"_testservice._tcp." name:identifier port:port];
 


Please note I have added comments of what always gets called and I have no issues with
     
-(void)listServices {
	bonjourServiceBrowser = [[NSNetServiceBrowser alloc] init];	
	[bonjourServiceBrowser setDelegate:self];
	[bonjourServiceBrowser searchForServicesOfType:@"_testservice._tcp." inDomain:@"local"];
}

- (void)netServiceBrowserWillSearch:(NSNetServiceBrowser *)browser {
	NSLog(@"Starting Search"); // ALWAYS CALLED
}
- (void)netServiceBrowserDidStopSearch:(NSNetServiceBrowser *)browser {
	NSLog(@"Stop Search"); // Only called when tell NSNetservicesBonjour to stop its search
}
- (void)netServiceBrowser:(NSNetServiceBrowser *)browser didNotSearch:(NSDictionary *)errorDict {
	NSLog(@"Warning Error"); // NEVER EVER CALLED
}

// This delegate actually always gets called... It always does detect the service when I start the client... but this is where I start the resolve
// process and hence the problem begins
- (void)netServiceBrowser:(NSNetServiceBrowser *)browser didFindService:(NSNetService *)aNetService moreComing:(BOOL)moreComing {
	  NSLog(@"Adding new service %@ %@ %@ %@ %@", aNetService, [aNetService addresses], [aNetService type], [aNetService hostName], [aNetService domain]);
	//[browser setDelegate:self];
        [aNetService setDelegate:self];
        // [aNetService resolve];
	[aNetService startMonitoring];
	[aNetService resolveWithTimeout:10];
	//[aNetService resolveWithTimeout:40];
}

// This also always gets called... it also detects the server dieing even if it did not properly resolve the service.
- (void)netServiceBrowser:(NSNetServiceBrowser *)browser didRemoveService:(NSNetService *)aNetService  moreComing:(BOOL)moreComing {
    NSLog(@"Removing service");
	[aNetService stopMonitoring];
}

// This is what I would expect to be called, but it is only called sometimes.... as explained above
- (void)netServiceDidResolveAddress:(NSNetService *)aNetService {
	NSLog(@"Resolved Servivce");
}

// This has been called only once... and it was when I attempted to resolve a service multiple times (recursively.. it was baaaad)
- (void)netService:(NSNetService *)aNetService didNotResolve:(NSDictionary *)errorDict {
	NSLog(@"Error Resolving %@", errorDict);
}

// This ALWAYS gets called.. even if the service does not send a message to the other two methods
-(void)netServiceWillResolve:(NSNetService *)sender {
	NSLog(@"RESOLVING");
}

// Never called
- (void)netService:(NSNetService *)aNetService didNotPublish:(NSDictionary *)errorDict {
    NSLog(@"Would not be here would it!?");
}
// Only called if the service resolved the first time through
- (void)netService:(NSNetService *)aNetService didUpdateTXTRecordData:(NSData *)data {
    NSLog(@"Called update");
}



Anyways sorry for the code.. its pretty bare.. can anyone spot anything crazy?


----
Feeling a bit stupid, but I fixed it by retaining the NSNetservice in the

- (void)netServiceBrowser:(NSNetServiceBrowser *)browser didFindService:(NSNetService *)aNetService moreComing:(BOOL)moreComing

That does not feel right though. Why should I have to retain that?

Is that by any chance a bug?

----

I spent a LOT of time with this stuff a few years ago.  I didn't document my train of thought or my sessions of trial and error, but here's what I can tell you based on the code that exists in my app as of today.

1) In netServiceBrowser:didFindService:moreComing I check to see if the discovered service is that of the current app (yeah, each publish discovers itself).  Since I have no reason to keep tabs on this service, I just return and bail out of the method.  Otherwise, I retain the NSNetService, set myself as its delegate, and call resolve.

Since the method above is a delegate method and does not create the NSNetService, it makes sense that it's set to autorelease.  In this method, you can either choose to do something with the service or not.  The "trick", it seems, is that the resolve may take longer that the current autorelease pool.  Correct me if I'm wrong, but this is how I interpret things.

(This code is simplified)
    
- (void)netServiceBrowser:(NSNetServiceBrowser *)browser didFindService:(NSNetService *)aNetService moreComing:(BOOL)moreComing {

	if(self serviceName] isEqualToString: [aNetService name) {
		return;
	} else {
		[self addPeeer: aNetService]; // this retains the service
		[aNetService setDelegate: self];
		[aNetService resolveWithTimeout: 5];
	}
}



2) After I call resolve (on the retained service), I get either a netService:didNotResolve or netServiceDidResolveAddress.  I just now noticed (as I write this) that I do not release my service in didNotResolve -- I don't think I've never been in this state  in 3 years since I show an error message here that I've never seen (heh).  Fixed.  But I digress.

In netServiceDidResolveAddress I do some ugly stuff with sockaddrs to get my IP address, make an NSSocketPort and NSConnection, and a bit of other fancy stuff I don't understand at this second.  After that's done, I [sender stop] and [sender release] (where sender is the NSNetService that resolved).

    
- (void)netServiceDidResolveAddress:(NSNetService *) sender {
	for( //fancy sockaddr stuff here to establish the connection... );
	
	// after NSConnection is established
	[sender stop];		// stop the resolve since the connection has succeeded
	[sender release];

	// retain the remote object and other stuff
}



So, at least in this context, the retain in didFindService makes a bit more sense.  The service has to be retained until the resolve completes (or fails), and after than you can dump it (unless you want to keep it around for the future, which there is nothing wrong with since it can be queried for information down the line that you may not want to keep track of now.  Just dump it when you're done with it).

---- 

Hey 

Thanks. I did not know about the stop. That might be a good thing to throw in. The retain did a large part of the fix for me, but I will add the stop.

*I'm fairly certain that at some point I discovered that a service can resolve multiple times.  This might occur if you are running multiple networks/protocols, though I can't remember how I noticed it.  Either way, once you establish a connection with a service, there's no reason to let it continue resolving.*

As for me thinking there was a bug cause I was not retaining...... I have no idea what I was thinking when I wrote that questions... Its not a bug. It made perfect sense that it was at the time, again no idea how I even came close to thinking about that.

----

It might be worth having a look at ComputersOnNetwork. I used the code from that page to get me started on Bonjour Networking.

