---
layout: page
title: NSPortCodersendBeforeTimesendReplyPort
---

Hi All,

I have an issue with following,  kindly let me know  any inputs on this.

I have created a sample  application that uses the distributed objects and Bonjour to communicate with each other instances of the application (sample) running
on other remote machine on the local network. Thought Bonjour successfully able to discover services , when I try to connect other machine it fails
(may be calls like ([NSConnectionInstance rootproxy]  ) with a error message in console looks like   "[NSPortCoder sendBeforeTime:sendReplyPort:] timed out ". But this is not  happening frequently.

Do you have any idea about this or Is this a bug in Distributed Object ? Any fix to this is highly helpful  for me.

(Please note: I have searched in the net , but could not get a proper fix to this)

Here is my code

    
//Server code
{
NSSocketPort *listeningSocket = NULL;
try 
{

listeningSocket= [[NSSocketPort alloc ] initWithTCPPort: 0];

}
catch (NSException *e ) 
{

NSLog(@"listening socket connot be created in server" );
exit(EXIT_FAILURE  );

}
//inet_ntop
struct sockaddr *addr;

addr = (struct sockaddr *)listeningSocket address] bytes];

uint16_t  port = 0;
if(addr->sa_family == AF_INET)
{

port = ntohs(((struct sockaddr_in *)addr)->sin_port);

}

else if(addr->sa_family == AF_INET6)
{

port = ntohs(((struct sockaddr_in6 *)addr)->sin6_port);

}
	

[[NSLog(@"listening port number in server %d ",port);

NSConnection *connection = [NSConnection connectionWithReceivePort:listeningSocket  sendPort:NULL];

[connection setRootObject: self];

[listeningSocket release ];

NSNetService *netService = [[NSNetService alloc ] initWithDomain:@"" type:@"_test._tcp" name:NSFullUserName() port:port];

NSRunLoop *currentRunLoop = [NSRunLoop currentRunLoop ];

[netService publish ];

[currentRunLoop run ];

//[netService release ];

[connection release ];
}

//Client code

- (void)netServiceDidResolveAddress:(NSNetService *)sender

{

NSLog(@"netServiceDidResolveAddress ..." );

NSArray *addresses;

addresses = [sender addresses];

if([addresses count] > 0) 
{

NSData *data =(NSData *) sender addresses] objectAtIndex: 0];

[[NSSocketPort *sendingSocket = [[NSSocketPort alloc ] initRemoteWithProtocolFamily: AF_INET 
												          socketType:SOCK_STREAM 
                                                                                              protocol:INET_TCP 
                                                                                                    address:data];

NSConnection *connection = [NSConnection connectionWithReceivePort: NULL sendPort:sendingSocket];

//[connection setRequestTimeout:20.0];

//[connection setReplyTimeout:20.0];

[sendingSocket release ];
try 
{

id tempProxy = [connection rootProxy ];

[tempProxy retain ];

[[NSNotificationCenter defaultCenter ] addObserver: self selector:@selector(connectionLost:) name:NSConnectionDidDieNotification object:NULL];

[tempProxy setClientName: self];

}
catch (NSException *e)
{

NSLog(@"proxy object creation failed" );

}

//[self createConnetion:[addresses objectAtIndex:0]];
}

[sender stop];		// stop the resolve since the connection has succeeded
				//[sender release];
} 


Thanks in advance,
Abu

