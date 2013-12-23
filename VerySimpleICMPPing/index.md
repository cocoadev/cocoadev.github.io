---
layout: page
title: VerySimpleICMPPing
---



For learning some basic networking skills, i'm trying to put together a small class that will let me perform some standard testing on a network like pinging and connecting to specific ports. I'm using the BSD Sockets API. Can anyone tell me the simplest way to ping a host to see if it is up? Cheers!

----

ICMP is not very simple to perform; you have to construct much of the packet yourself, and it's not that easy to listen for responses. The source code for     /sbin/ping is available and you'd probably be best off examining it. If your goal is simply to make something that works rather than learn all the nitty-gritty, you might actually be best off simply executing     /sbin/ping as a subprocess.

----

Thanks for the response! Will do that. And sending an ICMP echo request _is_ the simplest way to check if a given host is up, isn't it? Or is there an easier way, since this is really the result i'm after.

*Not all hosts support it, for example, my router is set to strip all ping packets.*

Hmm... You're right there... So is it possible to just send some kind of arbitrary packet and see if it "bounces off the walls" or get caught by somekind of network interface? I know it sounds quite noobish, but hey, i'm a noob on this area! Hope you get my point ;)

----

Sending an ICMP ping is the most "official" way, and probably the best, to see if a host is up. Another approach would be to open a TCP connection to some port on the host; if it's accepted or refused, then the host is up. If it times out, the host is probably down. However, these things can also be blackholed by a functioning host, so it's impossible to say for sure.

----

Just what i needed! I hadn't thought about the fact that the connection could actually be directly refused. I think trying to establish a connection will be sufficient for what my class should do. Thanks to everyone for the responses! It has been educating!

----

Although the ICMP "ping" was at one time the "official" way, more and more systems are set to either ignore it, or strip it altogether -- even on a LAN. For an Ethernet LAN the surefire way to see if there is an interface is an ARP. All systems MUST answer to an ARP. Unfortunately, this usually requires root access. And it is non-trivial for a noob.  General/LloydSargent

----

I needed to add ping support to my app - quickly. To see what I did have a look at General/SimplePing.
