---
layout: page
title: DistributedObjectsAndBonjour
---

Hi folks. Running into a perplexing problem here with an app I'm working on...

I've set up a scheme by which distributed objects are published and then listed via Rendezvous. It seems to work well enough in tests from local-machine to local-machine when I'm NOT connected to the net as I am now (via dialup). However it breaks down whenever I'm online, which is immensely disconcerting :)

The details - on publish I get my socket info all set up like so:

    
- (void)_rebuildLocalService {	
	if (localService) {
		[localService stop];
		[localService release];
		localService = nil;
	}
	
	if (localConnection != nil
		&& ([self name] != nil
			&& self name] length] > 0)) {
		
		[[NSSocketPort	*localPort	= (NSSocketPort    *)[localConnection receivePort];	
		struct sockaddr	*address	= (struct sockaddr *)localPort address] bytes];
		uint16_t		 port;
		
		if(address->sa_family == AF_INET) {
			[[NSLog(@"family is AF_INET");
			port = ntohs(((struct sockaddr_in *)address)->sin_port);
			
		} else if(address->sa_family == AF_INET6) {
			NSLog(@"family is AF_INET6");
			port = ntohs(((struct sockaddr_in6 *)address)->sin6_port);
			
		} else {
			// PUNT if we're publishing DO on some wierd interface...
			return;
		}
		
		localService = [[NSNetService alloc] 
		initWithDomain:@""
				  type:self netServiceBrowser] serviceType]
				  name:[self name] 
				  port:port];

		[[NSLog(@"registered local service with port %d", port);
		
		[localService setDelegate:self];
		[localService publish];
	}
}


I think that's pretty standard stuff... localPort is created when the DO connection is created itself, and is just the result of an [NSSocketPort port]. It's an local socket on an IPV4 address.

Where I seem to be getting in trouble is on the resolve side of the equation. Once I've resolved a service, I fetch its list of addresses. While I'm NOT connected to the net, I seem to get a list of two addresses - what I think corresponds to localhost as an IPV4, and something else that's larger and in disassembling it, is an IPV6 address. If I connect back through the small one, then I'm OK. If I AM connected to the net, I instead get a single address that identifies itself as IPV6. If I try to create an NSSocketPort with it, then the port ends up timing out on a send request when I'm trying to fetch a reference to the rootProxy.

    
			NSData			*addressData	= netService addresses] lastObject];
			struct sockaddr	*address		= (struct sockaddr *)[addressData bytes];
			uint16_t		family			= 0;
			uint16_t		port			= 0;
			
			if(address->sa_family == AF_INET) {
				[[NSLog(@"family is AF_INET");
				family	= AF_INET;
				port	= ntohs(((struct sockaddr_in *)address)->sin_port);
				
			} else if(address->sa_family == AF_INET6) {
				NSLog(@"family is AF_INET6");
				family	= AF_INET6;
				port	= ntohs(((struct sockaddr_in6 *)address)->sin6_port);
				
			} else {
				@throw [NSException exceptionWithName:@"VSUnknownAddressFamily"
											   reason:@"The address family is unknown"
											 userInfo:nil];				
			}
			
			NSLog(@"service is on port %d", port);
			NSLog(@"service is on host %@", [netService hostName]);
			

#if 0
			NSSocketPort	*sendPort	= [[[NSSocketPort alloc]
				initRemoteWithTCPPort:port host:[netService hostName]]
				autorelease];
			
			NSLog(@"port = %@", sendPort);
#endif
			
			NSSocketPort    *sendPort   = [[[NSSocketPort alloc]
			initRemoteWithProtocolFamily:family 
							  socketType:SOCK_STREAM 
								protocol:0
								 address:addressData]
				autorelease];
			
			NSConnection	*connection	= [NSConnection 
			connectionWithReceivePort:[NSSocketPort port]
							 sendPort:sendPort];
			
			[connection addRunLoop:[NSRunLoop currentRunLoop]];

			[connection setRequestTimeout:5];
			[connection setReplyTimeout:5];
			
			// NOTE: rootProxy = ... may throw on a connection timeout...
			NSDistantObject	*rootProxy = [connection rootProxy];



I figure that I might be able to get this to work if I can get things to work similarly to the canonical "DO over NSSocketPort" examples where I supply the hostname and port, rather than passing the address. Unfortunately, asking the service for its address returns the Rendezvous style address (eg. Silver.local.) rather than the more familiar IPV4 dotted-number notation. My sense is that this is blowing DO's mind.

Anyone run into this sort of problem before, or managed to avoid it altogether when fusing DO and Bonjour?

----

Addendum: Here's the output of a resolve attempt, with timeout exception caught:

    
2005-08-27 13:23:15.713 VSBlock[9631] RESOLVING....
2005-08-27 13:23:15.713 VSBlock[9631] addresses = (<1c1ed946 00000000 fe800000 00000000 00000000 00000001 00000001 >)
2005-08-27 13:23:15.714 VSBlock[9631] family is AF_INET6
2005-08-27 13:23:15.714 VSBlock[9631] service is on port 55622
2005-08-27 13:23:15.714 VSBlock[9631] service is on host Silver.local.
2005-08-27 13:23:17.027 VSBlock[9631] port = <1e01061c 1c1ed946 00000000 fe800000 00000000 00000000 00000001 00000000 >
2005-08-27 13:23:17.052 VSBlock[9631] CAUGHT EXCEPTION:[NSPortCoder sendBeforeTime:sendReplyPort:] timed out
2005-08-27 13:23:17.076 VSBlock[9631] WILL RE-RESOLVE



----

Hope the OP reads this. I tried parts of the sample code above because I wanted to learn Bonjour. 

My client would also hang on the request for the server's root proxy. After googling for about an hour I remembered that you have to allow iTunes sharing through the firewall on the host machine to share music locally. So, I just added a new service to the Firewall tab of the System Prefereneces for Sharing. I named this new service the same name of the NSNetService that publishes the DO connection on the server side. The TCP port number of the new service should be set to the value used to initialize the receiving connection's NSSocketPort (    initWithTCPPort:). --zootbobbalu 

----

Working on a similar problem, and getting a similar exception -"[NSPortCoder sendBeforeTime:sendReplyPort:] timed out" while trying to get the rootProxy - I realized that the problem had something to do with the server opening the connection for listening via family AF_INET but the client discovering the connection and trying to connect to the server via AF_INET6.  When I changed my Ethernet configuration (in System Preferences) so that TCP/IP "Configure IVv6" is "Off" (rather than "Automatically") then my client application started finding the server via AF_INET and getting the rootProxy works fine, no more exception.

Not sure exactly why, but at least I have a workaround.

