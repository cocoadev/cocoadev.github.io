---
layout: page
title: GettingTheRemoteProxyAddress
---

I'm using DistributedObjects with NSSocketPorts to build a fairly simple client server app.  I'd like the server to figure out when someone is connected to the server and what their IP address is.  What's the simplest way of recovering the remote client's info?

Follow up: Is there any way to use DistributedObjects AND know the IP address of of the remote proxy?  This has to work over TCP/IP.

----
What is your use-case for wanting the IP address? If it's for logging, I can see why you'd want it (though I don't know how to get it), but if it's for anything else, bear in mind (a) many users do not have a static IP, especially those using anonymous proxies, (b) IP addresses can easily be spoofed.

----
Exactly -- I'd like to log who is connecting to me.  I'm not sure what you mean by spoofing though.  The IP has to be valid in some sense because I have to be able to respond to that IP.  Sure, people could be using something like TOR and could be sitting behind giant private routers but for the most part the IPs should be fairly informative.

