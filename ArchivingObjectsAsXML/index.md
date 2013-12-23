---
layout: page
title: ArchivingObjectsAsXML
---



Can I somehow archive objects into XML? I don't like the NSData mess in my plist...

The objects I am archiving just hold an NSDictionary of properties, can't I have it archived as nice clean XML?

Thanks!

*Then archive the dictionary and make a method to create the object with that dictionary.*

Implement NSCoding and archive things however you like.

That doesn't work. NSCoder only archives objects as data, not as XML. I suppose you could extend NSCoder, but then you can't rely on the default     encodeWithCoder: and     initWithCoder: to use the right coder. However, if you are going to archive objects into a plist, it almost does make more sense to find the properties of an object and save those, rather than the whole object. Example plist file:
    
 +Root
 |
 +---SomeValue
 |
 +--+MyObjectProperties
    |
    +--+MyObject1
    |
    +--+MyObject2

...where     MyObject1 and     MyObject2 are dictionaries containing all the properties of the object (e.g., the name, the type, the delegate, whatever). --JediKnil

----

If you use NSKeyedArchiver, you can save it as XML. That's what IB does when you save as 10.2+ XML. I don't remember how it was actually done, maybe NSPropertyListSerialization was involved.

----

    [myKeyedArchiver setOutputFormat:NSPropertyListXMLFormat_v1_0];

