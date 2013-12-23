---
layout: page
title: MulticastSocketsQuestion
---



I'm going to be building an app that deals with transmitting audio from a central computer to other computer(s), starting with a one-to-one connection and building from there as this will be the first network-enabled app I've written.

I'm wondering if Multicast would be the best protocol to use, or standard TCP/IP? How is Multicast in terms of efficiency? Also, what would be a good socket wrapper to use? Omni's seems to be the most fully-featured but I worry that its size and dependencies could make for unnecessary overhead.

----

Multicast is basically dead. If this is your first network program, use TCP. It takes care of a lot of difficult things for you. Once you have a handle on that, then you can think about moving to UDP, using broadcast, etc.

----

You may want to take a look at RTP/RTCP and the multitude of associated standards (The focus is on VOIP, but they are related). Audio over TCP/UDP (done correctly) is a bit of work but manageable. If you focus on the standards now, it will save yourself a ton of trouble later.

----

Thanks for the advice. Just a couple more related questions: #1) Anyone know/have any sample code dealing with encoding sound and transmitting it over a network? #2) Would SmallSockets be up to the task, or would OmniNetworking be a better choice?

----

SmallSockets doesn't work in the background. AsyncSocket maybe?

