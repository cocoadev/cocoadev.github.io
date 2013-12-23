---
layout: page
title: ZeroConf
---

http://www.zeroconf.org/

Zeroconf is a proposed standard for bringing General/AppleTalk-style ease of use to typical IP networks. To make that a little bit clearer: it makes it tremendously easy to set up a self-configuring network. Nothing special required.

Zeroconf is not a protocol. It does not reinvent the wheel needlessly. It works on top of IP and doesn't say a thing about how you talk to hosts, all it does is let you find and publish them a lot more easily.

Basically, Zeroconf/General/RendezVous (Apple's implementation) adds three things to plain-vanilla/configure-till-you're-blue-in-the-face IP:


*automatic allocation of addresses without a DHCP server
*link-local hostname resolution without a DNS server
*automatic service discovery, without any kind of server at all


And what does that mean?

1) Zeroconf-enabled devices can give themselves an IP address, so you don't have to.

2) You can name your network's services without needing a DNS server.

3) You can browse a list of services on your network without having to poll each device and ask what services it supports. Service-oriented networking is kinder to users.

After you've established a connection with a host via General/ZeroConf, you can use socket communications, General/DistributedObjects, or any other IP-based protocol to talk to it. And your users don't have to type a thing.
