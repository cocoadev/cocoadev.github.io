---
layout: page
title: MakingObjectPropertyListCompatible
---

Hi,

I have a custom object and I would like it to be property list compatible, so I could add it to nsdictionary or nsarray and it would be human-readable. I guess NSObject's description should help me but how do I read object again from property list?

----

One way would be to design your custom object as a dictionary, or a wrapper around a dictionary. Then let Foundation do the heavy lifting for you; it would already know how to make your object into a PropertyList and then back into an object. Surely the data you hope to store can be represented as NSString and NSData.

----

See also AsciiPropertyLists, XmlPropertyLists, and ArchivingToAndUnarchivingFromPropertyLists.

