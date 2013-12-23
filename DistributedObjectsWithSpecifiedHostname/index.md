---
layout: page
title: DistributedObjectsWithSpecifiedHostname
---

I can't get any vended objects if I specify a hostname. Can anyone tell me what I'm doing wrongly ?

In my server application I vend an object as described in Apple's documentation 
( http://developer.apple.com/documentation/Cocoa/Conceptual/DistrObjects/Tasks/vending.html ).

    

- (void)awakeFromNib
{
    NSConnection *theConnection;

    theConnection = [NSConnection defaultConnection];
    [theConnection setRootObject:self];
    
    if ([theConnection registerName:@"ARP Application Server"] == NO) 
    {
	    /* Handle error. */
	    NSLog( @"Error while registering 'theConnection' as a named server. (ARP Application Server) " );
    }
}



This works fine.

On the client side I again used the code from Apple's website, this time for getting a vended object. 
( http://developer.apple.com/documentation/Cocoa/Conceptual/DistrObjects/Tasks/accessing.html )

    

- (void)awakeFromNib
{

    myApplicationServer = [[NSConnection rootProxyForConnectionWithRegisteredName:@"ARP Application Server" host:nil] retain];
    
//    [theProxy setProtocolForProxy:@protocol(ServerProtocol)];
}



This also works flawlessly as long as I use "host:nil" or"host:@"", which both makes the system look for the server on the local host. 

But if I specify a hostname as described in the dokumentation, it doesn't work anymore. 
I tried host:@"*" which is supposed to look for the server on all local hosts (I found on some other website about NextStep, so I'm not sure if it still works under Max OS X), as well as using the Rendezvous name and the global name of the computer. To be sure that I didn't misspell the hostname, I also tried host:[[NSHost currentHost] name]. Of course I tried host:@"localhost", too. In any of these cases the value returned to myApplicationServer is nil.

What did I do wrong ? I did activate the "Remote Apple Events" in the sharing preference pane and made sure that it is also enabled in the firewall settings. (Os version 10.3.4)

I want to use Distributed Objects to communicate between two machines, but how am I supposed to do so if it doesn't work on the localhost?

Can anybody help me to connect to an other computer without specifying that computer's name? 

----

Mach ports in OS X do not work for computer-to-computer communication, so DO does not work by default between computers. If you want to do remote DO, you have to use NSSocketPort and do some of the setup yourself. There is example code out there that shows how to set up a connection using NSSocketPort.

