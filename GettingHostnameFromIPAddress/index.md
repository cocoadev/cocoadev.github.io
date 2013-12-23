---
layout: page
title: GettingHostnameFromIPAddress
---

At some point, you may want to convert an IP address to a set of hostnames.  This is known as a reverse lookup; it is implemented using PTR records in your DNS server.  For example, say you have the IP address 17.149.160.49 and want to display its hostname(s) (apple.com).  You have a few options:



*     +[NSHost hostWithAddress:]
*     gethostbyaddr(3) - A BSD function



Both of these functions accomplish the reverse lookup by having the resolver (lookupd on Tiger and earlier; DirectoryService on Leopard) hit up DNS for a PTR record matching the IP address.  You can do the same from Terminal using     dig -x 17.149.160.49.  It's a very long list.

So that works for registered DNS names.  What about Bonjour (aka mDNS)?  Well, Bonjour is just a distributed multicast DNS system.  DirectoryService looks up entries in the local domain for you.  This is why you don't need to set up a DNS server but can still access all your machines with names like     kitchen-imac.local.  Bonjour also supports reverse lookups, but with one subtle caveat: your Mac wants to use IP<nowiki/>v6, not IP<nowiki/>v4.  This means reverse lookups against IP<nowiki/>v4 addresses in the local domain **may fail** if your network understands and prefers IP<nowiki/>v6.  This may be puzzling if you can verify that the IP<nowiki/>v4 address you have matches a computer whose name you know.

Try it for yourself;     mDNS(1) is a debugging tool that lets you see what's being advertised on your network.      mDNS -B _daap._tcp . will show you all the shared iTunes libraries on your network.  Then use     mDNS -L "Your Music Library Name" _daap._tcp . to see which IP addresses are advertised over Bonjour.

