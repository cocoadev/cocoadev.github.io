---
layout: page
title: ComputersOnNetwork
---

I'm writing a program which uses AppleScript to issue commands to a remote computer. I want to be able to find all of the computers on a network which support this. How would I do that? If you don't know how to do that, then can you tell me how to find all of the computers or devices connected to a network. Any help will be greatly appreciated.

----

check this out-> http://developer.apple.com/documentation/Cocoa/Conceptual/NetServices/

----

I was able to get this to work by finding all of the computers on the network using eppc (Apple Remote Events) by using the NSNetServiceBrowser class. Thanks for your help.

----

It would be nice if you could post your solution once you get it working. I'm sure it will be useful info!!

----

This code finds all of the computers on the network with Apple Remote Events (eppc) enabled and puts it into an NSTableView called remoteHostTableView.

    
@interface Controller : NSObject

{
    NSMutableArray *remoteHosts;
    NSNetServiceBrowser *serviceBrowser;
    IBOutlet NSTableView *remoteHostTableView;
}

@end


#import "Controller.h"

@implementation Controller

- (void)awakeFromNib
{
    remoteHosts = [[NSMutableArray alloc] init];
    serviceBrowser = [[NSNetServiceBrowser alloc] init];
    [serviceBrowser setDelegate:self];
    [serviceBrowser searchForServicesOfType:@"_eppc._tcp." inDomain:@""];
}

// NSNetServiceBrowser delegate methods
- (void)netServiceBrowser:(NSNetServiceBrowser *)aNetServiceBrowser
    didFindService:(NSNetService *)aNetService
    moreComing:(BOOL)moreComing
{   
    [remoteHosts addObject:aNetService];
    
    [aNetService setDelegate:self];
    [aNetService resolve];
    
    if (!moreComing)
    {
        [remoteHostTableView reloadData];
    }
}

- (void)netServiceBrowser:(NSNetServiceBrowser *)aNetServiceBrowser
    didRemoveService:(NSNetService *)aNetService
    moreComing:(BOOL)moreComing
{   
    [remoteHosts removeObject:aNetService];
    
    if (!moreComing)
    {
        [remoteHostTableView reloadData];
    }
}

// NSNetService delegate methods
- (void)netService:(NSNetService *)sender didNotResolve:(NSDictionary *)errorDict
{
    NSLog( @"Couldn't resolve %@", [sender name] );
}

- (void)netServiceDidResolveAddress:(NSNetService *)sender
{
    NSLog( @"Successfully resolved %@.", [sender name] );
}

- (void)netServiceWillResolve:(NSNetService *)sender
{
    NSLog( @"Attempting to resolve %@...", [sender name] );
}

// data source methods
- (int)numberOfRowsInTableView:(NSTableView *)aTableView
{
    return [remoteHosts count];
}

- (id)tableView:(NSTableView *)aTableView
    objectValueForTableColumn:(NSTableColumn *)aTableColumn
    row:(int)rowIndex
{
    return remoteHosts objectAtIndex:rowIndex] name];
}

@end


I have a new problem now. I'm trying to find the IP Address of the [[NSNetService after it is resolved so I can use it to tell the remote computer to do things in AppleScript using of machine. If you do [aNetService addresses], it returns an array of addresses, each is an NSData object. I'm just going to use the first address to attempt to make a connection. The problem is I can't convert the NSData object into the format that I need it in which is something like 192.168.1.1.

----

The sockaddr_in structure that the NSData object encapsulates is defined in the netinet/in.h header and there are some convenience methods for handling it in the arpa/inet.h header (both of these are found in /usr/include/).  Anyway, without further ado, a quick (as in entirely untested) category on NSData to extract the relevant bits would look something like this:
NSData+Extras.h
    
#import <Foundation/Foundation.h>

@interface NSData (Extras)
- (struct sockaddr_in)sockAddrStruct;
- (NSString*)ipAddress;
- (unsigned short)port;
@end

NSData+Extras.m
    
#import "NSData+Extras.h"
#import "sys/socket.h"
#import "netinet/in.h"
#import "arpa/inet.h"

@implementation NSData (Extras)
- (struct sockaddr_in)sockAddrStruct
{
	return *(struct sockaddr_in*)[self bytes];
}
- (NSString*)ipAddress
{
	return [NSString stringWithCString:inet_ntoa([self sockAddrStruct].sin_addr)];
}
- (unsigned short)port
{
	return ntohs([self sockAddrStruct].sin_port);
}
@end

*Edited the above code to add ntohs() around the return value of the port method.  This recently bit me on a new Intel machine.*

----

Since addresses could be either IPv4 or IPv6 I'd rather do something like

    
NSString *addressString = nil;
struct sockaddr *address;
unsigned int port = 0;

address = (struct sockaddr *) [data bytes];

switch( address->sa_family )
{
  case AF_INET:
  {
    struct sockaddr_in *ip4;
    char dest[INET_ADDRSTRLEN];

    ip4 = (struct sockaddr_in *) address;

    addressString = [NSString stringWithFormat: @"%s", inet_ntop(AF_INET, &ip4->sin_addr, dest, sizeof dest)];
    port = ntohs(ip4->sin_port);
  }
  break;

  case AF_INET6:
  {
    struct sockaddr_in6 *ip6;
    char dest[INET6_ADDRSTRLEN];

    ip6 = (struct sockaddr_in6 *) address;

    addressString = [NSString stringWithFormat: @"%s",  inet_ntop(AF_INET6, &ip6->sin6_addr, dest, sizeof dest)];
    port = ntohs(ip6->sin6_port);
  }
  break;

  default:
    NSLog(@"Unknown family");
    break;
}

