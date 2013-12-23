---
layout: page
title: AccessingNSDataFromPropertyList
---



I'm trying to find out if I can pull the NSData from a property list to get the original binary data back. I want to do this from a non-Cocoa (and non-Mac) environment. I've got the property list (a NSDictionary) which contains the NSData, which looks like base64 data (am I right?), so can I simply decode that somehow to get the data I want (assuming I'm correct that it is base64) from the plist?

*Not practical - it is base64 encoded, but the data is a stream of some sort, probably a CoreFoundation object, used to encode the data.*

That's not true at all.  The data is directly base64 encoded, so it's definitely possible to decode it into the original binary data.  I do this often on non-Mac platforms and I've never had a problem.

----

I think the above poster meant that the binary data is probably encoded by some CF object, so just base64-decoding the data doesn't help you much. Of course, only you can really tell that (by decoding the base64 data).

**That is what happened - I needlessly archived the data object before it was put into the plist. Not archiving it now allows me to decode it directly.**

