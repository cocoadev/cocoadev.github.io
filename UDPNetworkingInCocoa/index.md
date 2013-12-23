---
layout: page
title: UDPNetworkingInCocoa
---

Is there a way to start a UDP connection in Cocoa. I tried General/SmallSockets but I believe it is TCP/IP only.
----
I'm not aware of a UDP wrapper for Cocoa yet, but you can use BSD sockets from within Cocoa. Just make the necessary C calls and wrap them in Cocoa methods. If you don't know about programming with BSD sockets, read this. It's a great introduction and teaches you both TCP and UDP:

http://www.ecst.csuchico.edu/~beej/guide/net/

Good luck!
----
The General/CocoaAsyncSocket project contains an easy to use UDP sockets class:

http://code.google.com/p/cocoaasyncsocket/
