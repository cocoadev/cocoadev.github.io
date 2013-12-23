---
layout: page
title: NSHostBlocks
---



Anyone know why NSHost would block, especially when looking up the local host name or trying to get the currentHost addresses? Any way around it? Its causing my app to beachball everytime a lookup takes forever (only on certain network configurations).

----
Sounds like a DNS server isn't responding (or is misconfigured to an IP address that doesn't respond). In general it is bad to make synchronous calls to any networking from the main thread, because there's always the possibility that you will have delays. Try spinning your NSHost stuff into a secondary thread instead.

----
I think CFHost provides support for asynchronous address resolution.


----

But its a server, I need to look it up unfortunately,  its not in the main thread, but it still blocks the server continuing.

----
If you need the answer right away, then you're just screwed. DNS resolution can be slow, this is just a fact of networked life. If you actually need that response before you can continue, then you have no choice but to wait for it, however long it takes. This shows up in all kinds of real-world production servers; just try to ssh into a Mac OS X box with a fubared DNS configuration, and watch it time out on you.

If you don't need the answer right away, then the above advice still applies. Spin off another thread or look into CFHost.

----

Remember, I am only trying to look up the local host / currentHost, not some remote site. Anyway, I've found a workaround using BSD getifaddrs().

----
Instead of using ifaddrs you can also use the SystemConfiguration.framework API's to lookup the primary interface on the machine, and then use that to get the local IP address for that interface.  Or if you know the explicit interface you're interested in, you can just look that up.  The information in SC is cached, and you can also setup callbacks for when that data changes.  So if you're always looking up the same interface, you can just cache the results, and only change when the interface state changes.  Should be nice and fast....  :)

----
Do you have parallels installed? Try turning off the Parallels Host-Guest network port in Preferences.app
----

With Parallels Desktop 5 you need to do these steps:

1) Start Parallels Desktop if not already opened.

2) Open "Preferences" under "Parallels Desktop" menu.

3) Select the "Network" tab.

4) Check the "Show in System Preferences".

5) Close the "Preferences" dialog by clicking On "OK".

6) Open the "System Preferences" from the OS apple icon menu.

7) Choose the "Network" icon under "Internet & Wireless" section.

8) Select the "Parallel Shared Networking Adapter" from the list on the left.

9) change the "Configure IPv4" to "Off".

10) Click on "Apply" and close the "System Preferences".

If this doesn't solve the problem then repeat steps 1 to 3.

3.1) change "Connection type" to "Host-only Networking".

Repeat steps 4 to 9.

Now both "Parallel Shared Networking Adapter" are disconnected.

Continue to step 10 and problem solved.

