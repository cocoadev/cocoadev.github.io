---
layout: page
title: NetSocket
---

NetSocket is a free piece of code from BlackholeMedia.

An Objective-C TCP socket class that greatly simplifies asynchronous networking in Cocoa applications. NetSocket will do buffering of both reads and writes behind the scenes allowing you to use the data at the most convenient time and not worry if a call to send will  block or not. Through a series of optional callbacks, a delegate can be notified of socket events ( e.g. connected, disconnected, data available for reading ).

NetSocket uses new MacOS X networking APIs to make all of this possible, mainly CFSocket ( part of the CoreFoundation CFNetwork APIs ).

Check out http://www.blackholemedia.com/code/ for more information. Actually, it's mostly the same information just worded a little differently...but there is a download link! The NetSocket archive contains fairly good documentation on the class and the source is well commented.

CFSocket being fairly undocumented and all, we would much appreciate that you send in any changes you make to NetSocket. Fun!

----

Blackhole's site is down for some time now. You can download the original version from http://www.sebastianpape.de/archive/NetSocket.dmg.tar

----

Since Sebastian's download link is broken, the only way to still get the code is through WebArchive.org: http://web.archive.org/web/20071220205102/www.blackholemedia.com/code/

Alternately, Uli Kusterer has started a Github repository containing a slightly updated and renamed version of the class, which can be found at http://github.com/uliwitness/ULINetSocket

----

I agree - NetSocket does indeed rock, greatly simplifying the use of cocoa sockets.  I am, however, having a problem.

I can instantiate a socket and connect to a remote host.  I can then writeString::  to the socket and it works.  I then readString:  and that works.  So far, the proper delegates get called (socketConnected:, socket:dataAvailable:, socketSentData:).  So far, so good.

But when I now try to writeString:: to the socket it doesn't work.  When I [mySocket close] that doesn't seem to work either (the delegate socketDisconnected: doesn't get called and [mySocket opened] and [mySocket connected] both return YES.

Anyone with any experience with the NetSocket class have any idea what I'm missing?

Thanks in advance for any advice,

Phil


----

Have you checked to see if you have read everything before you write again?

Just a thought....

--zootbobbalu

----

oh yeah, you should add a method that lets you know if the socket is writable:

    

-(BOOL)isWritable {
    return mWritable;
}


--zootbobbalu
----

Still having the trouble.  What seems to be happening is this:

The socket executes readString:  ok.  And I can execute a writeString:: to it ONCE.  After that (even thought the socket still reports 'opened' and 'connected') it doesn't function as I would expect.

After the first writeString::  , the delegate dataAvailable::  still fires and I can continue to readString:  incomming data.  And though there is no error if I writeString::  or [mySocket close], neither of the delegates socketSentData:  or socketDisconnected:  seems to fire.  Also, the data doesn't seem to be sent to the socket it's connected to.

This test project I am using is bare-bones and I'm pretty sure there's nothing i'm doing wrong.  Perhaps I'm not understanding what the proper behavior of these NetSockets are.

Has anyone seen this behavior or had the opportunity to try it yourself?

Thanks,
Phil

----

I posted an update to NetSocket that should fix some problems people were experiencing...er, everyone was experiencing, including myself.

Thanks to everyone who submitted a bug report and sorry it took so long to get an update. Perhaps we should move NetSocket to CVS.

http://www.blackholemedia.com/code

- DustinMierau

----

New NetSocket works flawlessly. Awesome! I've been looking for something like this for a LONG time. I hate using CFSocket directly, it's so...hard. Thanks BlackHole Media!

Just wondering, is there a way to get the computer's local IP address with this? It's not absolutely, necessary, but it'd be nice for interface features.

-macfiend

----
Hello, this is but a bit offtopic; How do I know which version of NetSocket I have? I find no comment header or nothing. This might be fixed to next version ;) -- EnglaBenny
Oops, well it was so in the newer version I downloaded.. well, good job.

----
Here's a method you could add to get the computer's local IP addresses... TomWaters

    
#import <netinet/in.h>
#import <net/if.h>

#define IFCONF_BUFFERSIZE	4000
#define	max(a,b)	((a) > (b) ? (a) : (b))

+ (NSDictionary *)getIPAddresses
{
	NSMutableDictionary *ip_addrs = [NSMutableDictionary dictionary];

	char buffer[IFCONF_BUFFERSIZE];
	char *ptr = buffer;
	struct ifconf ifc = { IFCONF_BUFFERSIZE, { buffer } };

	int sockfd = socket(AF_INET, SOCK_DGRAM, 0);
	if (sockfd == -1)
		[NSException raise:@"NetSocket: Bad socket descriptor"
			format:@"NetSocket: Bad socket descriptor"];

	if (ioctl(sockfd, SIOCGIFCONF, &ifc) < 0)
		[NSException raise:@"NetSocket: Ioctl failed"
			format:@"NetSocket: Ioctl failed: %s", strerror(errno)];

	while (ptr < buffer + ifc.ifc_len) {
		struct ifreq *ifr = (struct ifreq *)ptr;
		struct sockaddr_in *sin = (struct sockaddr_in *)&ifr->ifr_addr;
		NSString *ipaddr = [NSString stringWithCString:inet_ntoa(sin->sin_addr)];
		char *cptr;

		// for next one in buffer
		ptr += sizeof(ifr->ifr_name) + max(sizeof(struct sockaddr), ifr->ifr_addr.sa_len);

		if (ifr->ifr_addr.sa_family != AF_INET) {
			continue;	// ignore if not desired address family
		}

		if ((cptr = (char *)strchr(ifr->ifr_name, ':')) != NULL) {
			*cptr = 0;		// replace colon with null
		}

		ioctl(sockfd, SIOCGIFFLAGS, ifr);
		if ((ifr->ifr_flags & IFF_UP) == 0) {
			continue;	// ignore if interface not up
		}
		[ip_addrs setObject:ipaddr forKey:[NSString stringWithCString:ifr->ifr_name]];

	}
	close(sockfd);
	return ip_addrs;
}



----

...or, um, <code>[[NSHost currentHost] address]</code>. You know, which ever's easier ;).

----

Actually, the above will return an arbitrary address according to the docs, which could be an IPv6 address, or the loopback address (127.0.0.1), etc. I wrote a quick hack that returns the first IPv4 address that's not a loopback.
    
-(NSString*)IPv4Address;
{
    NSEnumerator* e = [[[NSHost currentHost] addresses] objectEnumerator];
    NSString* addr;
    while (addr = (NSString*)[e nextObject])
    {
        if (addr componentsSeparatedByString:@"."] count] == 4 && ![addr isEqual:@"127.0.0.1"])
        {
            return addr;
        }
    }
    return [[[NSString stringWithFormat:@"127.0.0.1"]; // No non-loopback IPv4 addresses exist
}

In the case that there are multiple non-loopback IPv4 addresses, this will just return one of them arbitrarily. 
--AndySzybalski

----

Could anyone post some simple examples on how to use NetSocket. I can't get it to work. My client app shows that it sent data, but server never gets it. I can get connectionAccepted message. What could be wrong? I guess everything is fine with my network (DHCP).

----
 
Along those lines, I need a simple telnet client, so I can modify it for a couple things.  anyone care to give an example of how the telnet client is supposed to handshake (DO, WONT, WILL, etc) with NetSocket, so I can figure out if this is working or not?

----

Is there any reason to use NetSocket instead of NSFileHandle using sockets?

----

I am having a weird time with NetSocket!  the descriptions of the delegate methods in the documentation eg:

- (void)socket:(NetSocket*)inSocket dataAvailable:(unsigned)inAmount

is inconsistent with the actual netsocket.h, which declares this as: - (void)netsocket:(NetSocket*)inSocket dataAvailable:(unsigned)inAmount

also: I see what appears to be a fairly important element in the example [NetSocket scheduleOnCurrentRunLoop] that is not mentioned in the documentation either.  Not the easiest class to get started with due to all of this!

-EcumeDesJours

*Try my AsyncSocket. It's easy, maybe easier than NetSocket. -- DustinVoss*

----

The delegate netsocketDisconnected: does not get called after I call [socket close]. Is this how it is meant to work? It's not that big of a problem since I can just work around it, but it would be easier if it were called.

----

NetSocket is working great for me, except for one thing.  When a connection from behind a NAT router has the "NAT timeout of death" happen, NetSocket never seems to notice.  Even when you send additional text, it continues to act as though the socket is connected; you have to manually disconnect and reconnect.  Is there a known way around this, or am I gonna just have to start hacking my copy of NetSocket apart to figure out where it falls apart? :) 

----

Is there a way to add SSL support to NetSocket?
(I've also tried AsyncSocket as alternative but the download speed is lower than NetSocket with large amount of data)

