---
layout: page
title: BestServerConnection
---




    
NSSocketPort *port = [[[NSSocketPort alloc] initRemoteWithTCPPort:serverPort
                                  host:serverName] autorelease];
NSConnection *theConnection = [NSConnection connectionWithReceivePort:nil
                                  sendPort:port];

    
NSConnection *theConnection = [NSConnection
                           rootProxyForConnectionWithRegisteredName:@"svr" host:serverName];

    
NSSocketPort *port;
NSData *addr;
NSHost *host;
struct sockaddr_in sin;
struct hostent *ipaddr = NULL;
const char *ipstr = NULL;
unsigned long ip = 0;


host = [NSHost hostWithName:serverName];
		
if(host == nil)
{
	ipstr = [serverName cString];
	ipaddr = gethostbyname2(ipstr,AF_INET);
	if ( ipaddr != NULL )
	{
		ip = (*(struct in_addr *)(ipaddr->h_addr)).s_addr;
		memset (&sin, 0, sizeof(struct sockaddr_in));
		sin.sin_addr.s_addr = ip;
		sin.sin_port = serverPort ;
                sin.sin_family = AF_INET ;
		//memset ( sin.sin_zero , 0 , 8 ) ;
        
		addr = [NSData dataWithBytes:(&sin) length:(sizeof(sin))];
		port = [[NSSocketPort alloc] initRemoteWithProtocolFamily:AF_INET
                           socketType:SOCK_STREAM
                           protocol:IPPROTO_TCP
                           address:addr];
	}
}
NSConnection *theConnection = [NSConnection connectionWithReceivePort:nil sendPort:port];


Which three of these codes is the best to communicate with a objc server?

or are there others for objc/cocoa?

I am a newbie to NSConnection.

Tnx

*Please wait patiently for a response from someone more knowledgeable. I don't have the info you're asking for, but I keep seeing this page pop up with "anybody?" updates. If someone has an answer, they'll see your page and respond. Thanks!*

The first and third techniques are mostly equivalent (except the third won't work with IPv6, etc.). There's no reason to pick the more complicated way over the simpler way.

The second technique sets up an NSConnection over Mach ports and won't work for remote computers.

