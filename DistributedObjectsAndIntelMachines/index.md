---
layout: page
title: DistributedObjectsAndIntelMachines
---

I have a real headache of a problem that is boggling my mind.  I have an application that uses distributed objects and Bonjour to communicate with other instances of the application running on other machines on the local network.  The code that does this has been running well for over a year and I've never had any significant problems using DO before now.

Recently, two Intel-based machines (a new 20' iMac and a Mac Mini) have been added to the network.  My application compiles and runs perfectly well on these Intel machines.  However, the DO portion of the application seems irreparably broken when running on an Intel machine, and I'm pulling my hair out because I haven't changed a single line of code.

Here's the situation:
PPC apps can successfully resolve and communicate with other PPC instances.
Intel apps can successfully resolve and communicate with PPC instances.
PPC apps cannot obtain the rootProxy from Intel apps.
Intel apps cannot obtain the rootProxy from other Intel apps.

Here's the basic flow of my code (simplified for here).  I have an object that attempts to resolve services found by the service browser.
    
...
    [_netService setDelegate:self];
    [_netService resolveWithTimeout: 5];
...

netServiceDidResolveAddress: is called successfully and the service is resolved.
    
- (void)netServiceDidResolveAddress:(NSNetService *) sender {
	NSArray *addresses = [sender addresses];
	struct sockaddr * socketAddress;
	char buffer[256];
	uint16_t port;
	int count;
	
	// Search for the IPv4 addresses in the array.
	for (count = 0; count < [addresses count]; count++) {
		
		socketAddress = (struct sockaddr *)addresses objectAtIndex:count] bytes];
		
		// Only continue if this is an IPv4 address.
		if (socketAddress && socketAddress->sa_family == AF_INET) {
			
			if (inet_ntop(AF_INET, &((struct sockaddr_in *)socketAddress)->sin_addr, buffer, sizeof(buffer))) {
				
				port = ntohs(((struct sockaddr_in *)socketAddress)->sin_port);
				
				[[DLog(@"Resolved service: %@", [NSString stringWithFormat: @"%s:%d", buffer, port, nil]);

				// this works fine... there is never an error creating the connection
				NS_DURING
					NSSocketPort *socketPort = [[NSSocketPort alloc] initRemoteWithTCPPort:port host: [NSString stringWithCString: buffer]];
					_connection = [[NSConnection alloc] initWithReceivePort:nil sendPort:socketPort];
					[socketPort release];
				NS_HANDLER
					DLog(@"Error creating the connection. Bailing.");
					[self release];
					return;
				NS_ENDHANDLER
				
				// this throws an exception EVERY time when the NSConnection refers to an Intel machine
				NS_DURING
					_remoteObject = _connection rootProxy] retain];
				NS_HANDLER
					[[DLog(@"Error getting the root proxy");
					// no matter how long i wait or how many times i try, rootProxy never works
				NS_ENDHANDLER
				
				return;
			}
		}
	}
	[self release];
}

The call to [_connection rootProxy] fails every time with the following exception:
<code>
[NSPortCoder sendBeforeTime:sendReplyPort:] timed out
</code>
And this only happens with Intel machines, and it happens every time.  It doesn't appear to matter if the machine calling rootProxy is PPC or Intel, it fails whenever the remote machine is an Intel machine.  I should add that rootObject returns nil, even though it is simply not the case.

From the output you can see that it's getting a valid IP address and a valid port number.  I can confirm that they are the correct numbers for the remote machine.  Also, I'm not sure if this is of any consequence, but no matter the timeout settings on the NSConnection, I always receive the "timed out" error a fraction of a second after sending the rootProxy call.

    
2006-10-18 20:07:58.666 Reflection[26866] NetworkController.m:88 Resolved service: 192.168.1.106:2496
2006-10-18 20:07:58.840 Reflection[26866] [NSPortCoder sendBeforeTime:sendReplyPort:] timed out


If anybody has some insight into this please let me know.  This is absolutely crippling to the use of these newer Intel machines on the existing network, and I cannot figure out for the life of me what is going on.  Thanks in advance,

Jon

----
Here's more output from an Intel machine.  The test network contains one Intel machine and one PPC machine.

    
2006-10-18 20:57:14.663 Reflection[269] NetworkController.m:170 Set root object to: <NetworkController: 0x39d310>
2006-10-18 20:57:14.667 Reflection[269] NetworkController.m:199 Service published on port 5568 as "intel-imac.local:269"
2006-10-18 20:57:14.853 Reflection[269] NetworkController.m:90 Resolved service: 192.168.1.100:64048
2006-10-18 20:57:14.913 Reflection[269] NetworkController.m:118 Got remote object <NetworkController: 0x390a90>
2006-10-18 20:57:16.767 Reflection[269] NetworkController.m:90 Resolved service: 192.168.1.106:5568
2006-10-18 20:57:16.768 Reflection[269] NetworkController.m:116 Error getting the root proxy
2006-10-18 20:57:16.768 Reflection[269] NetworkController.m:118 Got remote object (null)


You can see the Intel machine (intel-imac.local) set its own root object and publish itself on lines one-two.  You can see it discover the G5 and get its rootProxy on lines three-four.  It then "discovers" itself but can't even get its own rootProxy (this case is skipped normally, but I've been testing various situations).  The same output from the G5:

    
2006-10-18 20:56:30.279 Reflection[27023] NetworkController.m:170 Set root object to: <NetworkController: 0x390a90>
2006-10-18 20:56:30.294 Reflection[27023] NetworkController.m:199 Service published on port 64048 as "power-mac-g5.local:27023"
2006-10-18 20:56:31.322 Reflection[27023] NetworkController.m:90 Resolved service: 192.168.1.100:64048
2006-10-18 20:56:31.332 Reflection[27023] NetworkController.m:118 Got remote object <NetworkController: 0x390a90>
2006-10-18 20:56:52.899 Reflection[27023] NetworkController.m:90 Resolved service: 192.168.1.106:5568
2006-10-18 20:56:53.060 Reflection[27023] NetworkController.m:116 Error getting the root proxy
2006-10-18 20:56:53.060 Reflection[27023] NetworkController.m:118 Got remote object (null)


The G5 can get its own rootProxy, but not that of the Intel machine.  This is driving me insane :(

----
Have you tried this with another Intel Mac? Have you tried disabling Mac OS X's firewall on the Intel Mac? It's possible that the firewall is interfering with the DOs. Just a thought--I don't work with DOs often. :)
----
Yup, I have 2 Intel Macs, and 2 PPC Macs here setup in my office now (it's a mess).  No firewalls enabled on any of them, all connected by a boring router with nothing other than themselves connected. The behavior is entirely reproducible and only effects the Intel machines.

----
Please excuse if you have already looked into this or if I am not very helpful.  But the fact that it seems only requesting information form the Intel machines is giving a problem implies that this has something to do with the very few architectural differences noted by apple which they haven't automatically handled for programmers.  Specifically in this case it looks like it might be a byte-ordering issue.

<code>
Network-Related Data
Network-related data typically uses big-endian format (also known as network byte order), so you may need to swap bytes when communicating between the network and an Intel-based Macintosh computer. You probably never had to adjust your PowerPC code when you transmitted data to, or received data from, the network. On an Intel-based Macintosh computer you must look closely at your networking code and ensure that you always send network-related data in the appropriate byte order. You must also handle data received from the network appropriately, swapping the bytes of values to the endian format appropriate to the host microprocessor.

You can use the following POSIX functions to convert between network byte order and host byte order. (Other byte-swapping functions, such as those defined in the OSByteOrder.h and CFByteOrder.h header files, can also be useful for handling network data.)

network to host:
uint32_t ntohl (uint32_t netlong);

uint16_t ntohs (uint16_t netshort);

host to network:
uint32_t htonl (uint32_t hostlong);

uint16_t htons (uint16_t hostshort);

</code>

more of the same can be seen in the Universal binary programming guide http://developer.apple.com/documentation/MacOSX/Conceptual/universal_binary/index.html#//apple_ref/doc/uid/TP40002217

Anyway, many of us have DO in parallel situations and I for one am very interested to see how you resolve this.  Good luck!

-Stephen
----
Thanks for the input.  The thing is, the behavior I am experiencing happens before I can even attempt to send any data over the connection.

Essentially, it boils down to the server doing: <code>
[theNSConnection setRootObject: theObjectToVend];</code>

and the Client unable to call:<code>
theDO = theNSConnection rootProxy] retain];</code>

As far as I can discern, the only places where I need 'ntox' I have them in place.  For instance,    
if (inet_ntop(AF_INET, &((struct sockaddr_in *)socketAddress)->sin_addr, buffer, sizeof(buffer))) {
port = ntohs(((struct sockaddr_in *)socketAddress)->sin_port);


Other than those two lines, all my connections and ports are created using [[NSSocketPort and NSConnection initializers.  I'll dig more into those initializers; maybe one isn't doing something I am assuming it is doing on the Intel boxes.
----
I got it. Both [[NSNetService alloc] initWithDomain:type:name:port:] and [[NSSocketPort alloc] initRemoteWithTCPPort:host:] require ports in host order.  When I was publishing, I would get a port from [[[NSSocketPort alloc] initWithTCPPort: 0] address], from which the extracted port was in network byte order.  Sending [[NSNetService alloc] initWithDomain:type:name:port:] a port in network byte order leads to something other than what I was expecting on Intel; a problem that never appeared in PPC.

I feel like an idiot now, but the problem is solved.  Thanks for the pushes in the right direction.

----
So did you have to reverse the byte-order of the port or what?  Could you just write up the function you used to convert your ppc port to an intel port?  I think many people will need just such a function.

-Stephen
----
Sure. The whole problem stemmed from the way I obtained my original port for the connection.

Firstly, I use a category on NSData to get information out of a sockaddr_in structure.  The important methods (and the key to the problem) are:
    
#import "NSData+SockAddr.h"
#import "sys/socket.h"
#import "netinet/in.h"

@implementation NSData (SockAddr)
- (struct sockaddr_in)sockAddrStruct
{
        return *(struct sockaddr_in*)[self bytes];
}
- (unsigned short)dataPort
{
        // this ended up being the key... I didn't have the ntohs() here previously
        return ntohs([self sockAddrStruct].sin_port);  
}
@end

So my category was returning a network byte ordered port number.  Network and Host order are the same on PPC... not so on Intel.
Then the code that sets up the connection.
    
	NSNetService *_netService;	// both of these are in my header file
	NSConnection *_connection;
	...	
	NSSocketPort *receivePort;
	NS_DURING
		receivePort = [[[NSSocketPort alloc] initWithTCPPort: 0] autorelease];
	NS_HANDLER
		NSLog(@"Server: Unable to obtain a port from the kernel.");
	NS_ENDHANDLER
    
	uint16_t chosenPort = receivePort address] dataPort];
    
	// Create the Connection
	NS_DURING
		_connection = [[[[NSConnection connectionWithReceivePort: receivePort 
												 sendPort: nil] retain];
	NS_HANDLER
		NSLog(@"Server: Error creating the Connection.");
	NS_ENDHANDLER

	// Set the root object
	[_connection setRootObject: self];

	// Create the NetService
	_netService = [[NSNetService alloc] initWithDomain: @"" 
								 type: @"_yourservice._tcp." 
								 name: [NSString stringWithFormat:@"%@:%d", [[NSProcessInfo processInfo] hostName],
									[[NSProcessInfo processInfo] processIdentifier], nil]
								 port: chosenPort];
	// Publish
	[_netService setDelegate: self];
	[_netService publish];

I think that's the gist of it.
----
If it may help, I think your code is too complex. The NSNetService object already provide the hostname and port information that you need to initialize the NSSocketPort. This version uses much less code and it's equivalent (tested):
    
- (void)netServiceDidResolveAddress:(NSNetService *)netService
{
	NSLog(@"Resolved service: %@:%d", [netService hostName], [netService port]);
		
	@try
	{
		NSSocketPort *socketPort = [[[NSSocketPort alloc] initRemoteWithTCPPort:[netService port] host: [netService hostName]] autorelease];
		_connection = [[[NSConnection alloc] initWithReceivePort:nil sendPort:socketPort] autorelease];
		
		_remoteObject = _connection.rootProxy;
				
		return; // Everything ok
	}@catch(NSException *e)
	{
		NSLog(@"Received an exception: %@", e);
	}
}


Very simple, uh? This way nobody has to deal with byte ordering.. 

- Nicola
----

@Nicola, the NSNetService' port is available only on Mac OSX 10.5 or later, so for previous version we have to deal with byte ordering...

- Sanniv

