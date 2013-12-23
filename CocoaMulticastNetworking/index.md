---
layout: page
title: CocoaMulticastNetworking
---

Hello. I wish to send multicast to specified multicast address (f.e. 224.10.12.1/999). How to do this via obj-c/MacOS X? Without using BSD layer?
----
Have you tried using a network library to send to that address as if it was a normal address? Does that work?
----
According to the article at stepwise (http://www.stepwise.com/Articles/Technical/2003-04-22.01.html ) OmniNetworking is the only Cocoa networking framework that directly supports Multicast, though any of them that support UDP could theoretically be used with a smidgen of BSD Sockets code.  You could also use Apple's CFSocket API if you're comfortable with CoreFoundation.  -- Bo

