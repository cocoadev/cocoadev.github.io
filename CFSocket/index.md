---
layout: page
title: CFSocket
---

CFSocket documentation: the header file, and

file:///Developer/ADC%20Reference%20Library/documentation/CoreFoundation/Reference/CFSocketRef/index.html
http://developer.apple.com/documentation/CoreFoundation/Reference/CFSocketRef/index.html

Alternatively, try the **macnetworkprog** archives at http://lists.apple.com.

See also CFNetwork, SocketClasses.

----

CFSocket's only purpose in life is to make BSD sockets send events to a RunLoop. The other practical option would be to use various threads, but using run-loops in this fashion can be more efficient for single-processor machines.  It is also easier to debug.

If you want to do reading and writing to the socket, use the BSD functions or CFStream.

Before Tiger, you could use CFStream in conjunction with CFSocket without difficulty, keeping in mind that both have read and write run loop events, which for all I know might both be posted at the same time. See the EchoServer sample project.

But in Tiger, they changed the way CFStream does its callbacks, and it can no longer be used with CFSocket.

-- DustinVoss

----

Some notes:


*kCFSocketConnectCallBack's error code is a POSIX error.
*CFSocketCopyAddress returns different addresses depending on which network interface the socket is on. For example, if you connect to another port on localhost, this function'll return 127.0.0.1. If you connect to a computer on the Internet, it'll return your IP address.
*I heard that CFSocketCreateConnectedToSocketSignature has a bug. Apple knows about it. I don't know if or when they fixed it.
*CFSocketSetAddress effectively does both bind() & listen().


----

I'm trying to find out how much data (bytes or packets, I don't care) has been read by a plugin connected to my application. I can get the details of the connection, IP addresses, protocol and port numbers using the **lsof (1)** command-line tool. Knowing which port is open (on my process!) I should be able to monitor how much data is being transferred. Any ideas?

Kevin Patfield

----

I assume you want to do this as a user, not programmatically. If so, install fink and X11 if you haven't, then install ethereal, which is a wonderful free packet sniffer. You can filter the display so it only shows the relevant packets, and you should be able to get a total.

If you want to do it programmatically, 'man pcap', and get ready to have some fun writing an IP packet parser. Another approach would be to require that the plugin go through your app to make network connections, allowing you to intercept the data on the way. This would be impossible (or nearly) to force, though.

----

No, I want to do this programmatically and while pcap seems to provide just the capabilities I was looking for, it requires root access. Users of my application will not necessarily have an admin password. Is there another way?

Kevin Patfield

----

I have a rather large library that negotiates communication between a server and clients. The sockets use the kCFSocketDataCallback flag to enable getting data out of the connection. The library has been working very well for a long time. Now i am trying to load it up into a PPC app that runs under rosetta on intel machines. The load goes fine and the sockets can connect and send data but they never receive anything. I've tried everything i can ( giving a bit of love to the run loop, etc, .. ) but cannot understand whats going on. Are there any know issues relating to CFSockets and PPC on intel machines?

