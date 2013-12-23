---
layout: page
title: IPAddressOfService
---

I have an NSNetService object and have resolved it. I need to find out the IP of the NSNetService so that I can use it in an AppleScript in a format like 192.168.1.1. Can someone please tell me how to do this. Thanks in advance.

----
I think this will do:
    
NSData *addressData = theNetService addresses] objectAtIndex:0];

struct sockaddr_in *socketAddress = (struct sockaddr_in *)[addressData bytes];
int firstOctet = (socketAddress->sin_addr.s_addr & 0xFF000000) >> 24;
int secondOctet = (socketAddress->sin_addr.s_addr & 0x00FF0000) >> 16;
int thirdOctet = (socketAddress->sin_addr.s_addr & 0x0000FF00) >> 8;
int fourthOctet = (socketAddress->sin_addr.s_addr & 0x000000FF) >> 0;

[[NSString *ipAddress = [NSString stringWithFormat:@"%d.%d.%d.%d", firstOctet, secondOctet, thirdOctet, fourthOctet];

You probably want the port number too:
    
UInt16 port = socketAddress->sin_port;


You will have to #import <sys/socket.h> and #import <netinet/in.h> for this to work.

----
For another solution, see ComputersOnNetwork.

