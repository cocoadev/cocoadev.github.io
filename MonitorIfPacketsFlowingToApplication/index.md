---
layout: page
title: MonitorIfPacketsFlowingToApplication
---

My application needs to check if packets are being streamed from a particular external site or not. At the very least I need to be able to check whether a plug-in of my own application is receiving packets but I'd also like to be able to monitor another application in this way. The application would be on the same host and owned by the same user so security *shouldn't* be a propblem. While I'd prefer Cocoa classes or C functions to do this, I'm not averse to running external commands via NSTask.

This is what I've found out so far:

I can use **lsof(1)** to determine if the target application has a live connection using the **-i** option. For example **lsof -i:rtsp** will tell me about any rtsp streams that are open. I can parse the standard output of this command within my application to determine local port numbers, application names, PIDs, file numbers, etc.

**netstat(1)** will tell me if packets are flowing by using the **-r** option. Unfortunately this only seems to tell me about the total number of packets flowing over a network interface (such as **en0**) and not which connections they are part of.

My plan was to use **pcap(3)**, filtered for the connection of interest, to check the packet counts. Unfortunately pcap requires my application to be run as root, which isn't practicable.

Is there some other way to determine whether packets are flowing over a particular TCP connection? I don't need to be able to look inside the packets, only to get an indication that they're flowing.

Thanks.

Kevin Patfield

