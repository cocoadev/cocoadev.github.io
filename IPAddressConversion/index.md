---
layout: page
title: IPAddressConversion
---


When I include the code
    
	NSString *myAddress = [[NSHost currentHost] address];
	
	NSLog(@"myAddress = %@", myAddress);


I get the following output:
    
myAddress = fe80::214:51ff:fe7a:3ba7%en1


How would i convert that into an NSString, as a dot separated decimal IP address?

----

That is the IPv6 address, unless there is an equivalent IPv4 address you are out of luck.

----

I suspect there is because my computer's network settings give me an IP that I can use.  Now what?

----

Here is a way to do it using C code:
    
char *GetPrivateIP() {
	struct hostent *h;
	char hostname[100];
	gethostname(hostname, 99);
	if ((h=gethostbyname(hostname)) == NULL) {
        perror("Error: ");
        return "(Error locating Private IP Address)";
    }
    return inet_ntoa(*((struct in_addr *)h->h_addr));
}


If you are O.K. with doing it like that, remember to add these includes (not imports) to the top of your header file:

    
#include <netdb.h>
#include <netinet/in.h>
#include <arpa/inet.h>


If you want an NSString in the end, instead of a character array, just use this:

    
NSString *ip = [NSString stringWithCString:GetPrivateIP()];


*Actually,     #import is fine even for plain C headers.*

----

You can also use NSHost's -addresses. That will give you an array of addresses, some of which will be IPv4. But keep in mind that a computer can have more than one address, even IPv4 address, for example a VPN address and a normal Ethernet address. The "proper" way to do this kind of inquiry is with the System Configuration Framework, but I for one don't understand it. Someone needs to write a nice Cocoa wrapper for it� �DustinVoss

See also GetIPAddress.

----

to DustinVoss:
this maybe not in cocoa scope but i'm curious how to know whether an ip is in some range. for example :

ip : 192.168.0.5
range start : 192.168.0.1
range end : 192.168.0.10

this output true. I ever try using the 'inet' function in c but i don't quite understand how to use it. Thanks - JuliusJuarsa

----
There is no built-in way to see if an IPv4 address string is within the range of another IPv4 address string. You have to write the function yourself. You'll need to turn the string into four numbers and check them. You can use inet_aton() to turn the string into an in_addr struct. The in_addr struct has one member, s_addr. s_addr is a four-byte integer made from the four bytes of the IP address, but it is in network byte order. You need to convert that into host byte order with ntohl(), and then you can compare it like any other integer.

