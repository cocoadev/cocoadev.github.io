---
layout: page
title: NSCopying
---

A formal protocol objects can adopt to make copies of themselves. From Apple's docs:

*
The NSCopying protocol declares a method for providing functional copies of an object. The exact meaning of �copy� can vary from class to class, but a copy must be a functionally independent object with values identical to the original at the time the copy was made. A copy produced with NSCopying is implicitly retained by the sender, who is responsible for releasing it.
*

[http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Protocols/NSCopying.html]

Here's a great page detailing how you might go about implementing -copyWithZone:
[http://developer.apple.com/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmImplementCopy.html#//apple_ref/doc/uid/20000049] 
[http://www.jinnlife.com]


----

For threaded discussion, see ImplementingNSCopying

