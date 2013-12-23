---
layout: page
title: UPnPBroadcastNotify
---

I am trying to write General/UPnP support for my app, and I seem to be running into some trouble.  The code I have broadcasts, but the General/UPnP device I am using will not respond.  I know the message is correct, as I have copied the message from a working General/UPnPMediaServer app.  Below is the code for the broadcast of Notification.  This is to show the device that my app is a Media Server.  It broadcasts, but it will not make a TCP request for the file path included.  I think it may be that I need to set some additional socket flags, but I do not know what they may be.  Thanks for any help.

variable a through e are char **s with the messages. Here is an example of one.

    
char *a = 
	"NOTIFY * HTTP/1.1\r\n"
    "LOCATION: http://192.168.50.108:10002/\r\n"
    "HOST: 239.255.255.250:1900\r\n"
    "SERVER: POSIX, General/UPnP/1.0, General/MyProgram/1.1.7\r\n"
    "NTS: ssdp:alive\r\n"
    "USN: uuid:12345678-1234-1234-1234-123456789012::upnp:rootdevice\r\n"
    "CACHE-CONTROL: max-age=1800\r\n"
    "NT: upnp:rootdevice\r\n"
    "Content-Length: 0\r\n\r\n";



    
void udpBroadcast() {
    char *theMessage[5];
    theMessage[0] = a; theMessage[1] = b; theMessage[2] = c; theMessage[3] = d; theMessage[4] = e;
    for (int message = 0;message<5;message++) {
       unsigned int ss = socket(AF_INET, SOCK_DGRAM, IPPROTO_UDP);
	if (ss == INVALID_SOCKET) {
		printf("socket: error");
		return;
	}

    	for (int repeat = 0; repeat < 2;repeat++) {			
			int on = 1; 
			int ret = setsockopt(ss, SOL_SOCKET, SO_BROADCAST, &on, sizeof(on));
			
			if (ret == -1) {
				printf("setsockopt: error");
				return;
			}
			struct sockaddr_in sin;
			memset(&sin, 0, sizeof(sin));
			sin.sin_family = AF_INET;
			sin.sin_port = htons(1900);
			sin.sin_addr.s_addr = inet_addr("239.255.255.250"); 
			int n = sendto(ss, theMessage[message], strlen(theMessage[message]), 0, (struct sockaddr *) &sin, sizeof(sin));
			printf("Message sent, return value: %d\n",n);
			shutdown(ss, 2);
			close(ss);
		}
	}
}


This runs perfectly, and sends messages correctly, but the General/UPnP device will not respond.

Thanks,
Mike

p.s. I know it's not Cocoa, but it's for a Cocoa app, and it must be socket code because it will be used cross-platform.


----
Why don't you use available General/UPnP librairies?
 
 *General/CyberLink for C++ (Open Source) - http://sourceforge.net/projects/clinkcc/
 *General/CyberLink for Java (Open Source) - http://sourceforge.net/projects/cgupnpjava/
 *General/UPNPLib (Open Source Java) - http://www.sbbi.net/site/upnp/
 

Might not suitable for your project, but still interresting:
 
 *libupnp (Open Source) the most used ,but not sure about OS X availability - http://upnp.sourceforge.net/
 *General/UPnP AV SDK by General/TwonkyVision (Commercial) - http://www.twonkyvision.de/General/UPnP/avsdk.html
 *Pure Perl General/UPnP (Open Source) - http://sourceforge.net/projects/perlupnp
 

General/StephaneBoisson

----
General/UPnP uses multicasts rather then broadcasts (although your code would probably still work). Also, you close your socket after sending your first NOTIFY for your root device. So I doubt you sent out anything beyond that first NOTIFY broadcast. I don't know what device you have, but the root device is usually uninteresting, so it could be that the device just didn't see anything that it wanted to respond/connect to. Lastly, you don't need to be creating a new socket for every message, you can reuse them. You should probably try something like this: 

    
void udpBroadcast()
{
	char *theMessage[5];
	theMessage[0] = a; theMessage[1] = b; theMessage[2] = c; theMessage[3] = d; theMessage[4] = e;

	// create socket
	unsigned int ss = socket(AF_INET, SOCK_DGRAM, IPPROTO_UDP);
	if (ss == INVALID_SOCKET)
	{
		printf("socket: error");
		return;
	}   
	// set multicast, with a time to live of 4 hops, as recommended by the General/UPnP Spec
	int ttl = 4;
	int ret = setsockopt(ss,IPPROTO_IP,IP_MULTICAST_TTL,&ttl,sizeof(ttl));
	if (ret == -1) 
	{
		printf("setsockopt: error");
		close(ss); // don't leak sockets...
		return;
	}
	// Setup the General/UPnP broadcast address
	struct sockaddr_in sin;
	memset(&sin, 0, sizeof(sin));
	sin.sin_family = AF_INET;
	sin.sin_port = htons(1900);
	sin.sin_addr.s_addr = inet_addr("239.255.255.250"); 
	
	// send each message 2 times
	for (int message = 0; message < 5; message++)
	{
		for (int repeat = 0; repeat < 2; repeat++)
		{		
			int n = sendto(ss, theMessage[message], strlen(theMessage[message]),
						   0, (struct sockaddr *) &sin, sizeof(sin));
			printf("Message sent, return value: %d\n",n);
		}
	}
	// close the socket after we have sent all the multicasts
	close(ss);
}


Michael

P.S. Use Ethereal (http://www.ethereal.com/) to verify what is going out on the wire, is what you wanted to send.
