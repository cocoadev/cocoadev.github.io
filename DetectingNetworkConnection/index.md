---
layout: page
title: DetectingNetworkConnection
---

Apple Software Update say: "Checking occurs only when you have a network connection" How can I detect this?

It is easy to load something in the background:
    
url = NSURL alloc] initWithString:urlString];

// but this will block if the network connection is down!
[url loadResourceDataNotifyingClient:self usingCache:YES];


I solved it by forking a new server thread, and running this code from the new thread. But there must be a way to detect the network connection.

I tried to use [[NSHost to do a quick check for the network connection, before trying to download:
    
NSHost *host = [NSHost hostWithName:[url host]];
if ([host address] != nil) {
    // Download code here...
}


But this would block also for a minute or so before failing.


-- Nir

----

