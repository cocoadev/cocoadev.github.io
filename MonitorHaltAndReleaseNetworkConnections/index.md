---
layout: page
title: MonitorHaltAndReleaseNetworkConnections
---

Hi there!

I'm trying to write a cocoa app that will monitor all outgoing network connections to a particular address, or collection of addresses. When a connection occurs, it will hold the connection and ask for your permission for it to continue. Depending on the choice, the app will terminate the connection or let it continue. Before you say Little Snitch, I want to be able to monitor local network connections rather than WAN connections.

I have researched various unix tool's and came up with using tcpdump which tends to work nicely inconjunction with grep. Is this the optimum tool to use? I also need some advice on how I would go about getting my program to attach itself to a network connection and control it.

Any ideas?

----

I don't think using tcpdump + grep is the best way to do this. Possibly leveraging the bpf (berkeley packet filter) that goes on behind the scenes of tcpdump might be a good way to detect these outgoing connections, but bpf tends to work on the assumption that you're just watching, so there's no way for you to hook in and "pause" a connection request. What you really need to do is hook into the networking stack and look for outgoing TCP SYN packets going to the specified IP address. I'm not sure how little snitch handles this. OSX has a built-in firewall that might have hooks to allow you to do this without writing lowish-level code, but I'm not sure how to interface with it. The program OSX comes with (command-line, with GUI in the sharing panel) is called ipfw, if you want to look into its documentation.

Hope this helps!
--DanielPeebles

----

The only way you are going to be able to *intercept* outgoing connections, as opposed to simply watching them, is by writing a kernel extension or using something like ApplicationEnhancer. (And there will be ways for a devious app to get around any ApplicationEnhancer patching.) Tcpdump simply won't do it. You can watch things easily enough but you can't modify the traffic.

