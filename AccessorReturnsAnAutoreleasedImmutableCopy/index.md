---
layout: page
title: AccessorReturnsAnAutoreleasedImmutableCopy
---

If I return an immutable copy of a mutable *instance* variable, should I use something other than an accessor?

Clients of my class may feel entitled to think that as long as they have an instance of the owner,
they have a valid copy of the ivar - without any extra effort on their part, if they get it from an accessor.

Is part of the contract between me and the client that they will not try to     release an object they get via an accessor?

If I return the copy without an     autorelease, clients of the class will get a memory leak.
If I return the copy with an     autorelease, clients may try to use the object without retaining it and crash.

Or should I jusst publish a method in my interface that no one can mistake as being a standard accessor
and force them to use that. What naming convention should I use for such a method?

Would     - ( NSString * ) stringWithCopyOfIvarString be generally understandable in this context?

Here, at least, clients will be clued in that they are getting a standard kind of autoreleased object.
----
* Its generally assumed that it will be autoreleased, so you could ignore this problem or document it. *

----
Aaah! MultipleEditSyndrome! From Apple's MemoryManagement docs:

*
This document summarizes the rules for memory management in Objective-C.

If you get an instance of an object via a method with     alloc or     copy in its name (or from the NSObject convenience method new), or if you send an instance a     retain message, you have a reference to it and will need to release it eventually.

If you get an instance of an object any other way (exceptions include multithreaded applications and some Distributed Objects situations), you must NOT release it and you must NOT hang onto it beyond the scope of the current method (if you need to hang onto it, retain it).

    autorelease just means *release this later* (for some definition of later).
*

Also (code removed, documentation changed to reflect that):

*
It is common practice in Objective-C code to copy value objects that represent attributes. C-type variables can usually be substituted for value objects, but value objects have the advantage of encapsulating convenient utilities for common manipulations. For example, NSString objects are used instead of character pointers because they encapsulate encoding and storage. Despite NSString functionality, the role played by NSStrings parallels the role played by character pointers.

When value objects are passed as method arguments or returned from a method, it is common to use a copy instead of the object itself. Storing a copy of the variable has the effect of producing an object that is independent of the original, but has the same contents. Subsequent changes to the copy don't affect the original, and changes to the original don't affect the copy. Similarly, it is common to return a copy of an instance variable instead of the instance variable itself.
*

----

OP Again: That's what I thought. I've read all that, too.

But many textbook examples simply return objects from their accessors that are not autoreleased... or copied.

Not that I should regard a textbook example as gospel.

But I guess that goes right to the spirit of: 

*
If you get an instance of an object any other way (exceptions include multithreaded applications and some Distributed Objects situations), you must NOT release it and you must NOT hang onto it beyond the scope of the current method (if you need to hang onto it, retain it).
*

Which is what I tend to keep in mind.

----
From your comments, it still seems that you mix the autorelease and the copy things. They are completely separate issues. When you return an object, it should not come with any retain issues attached to it, except if an init or copy method. So accessors should always return an object that will leave through the end of the autorelease cycle.

When you do copy the ivar, then you need to also autorelease it, because 'copy' does increase the retain count, and you want to be even.

When you do not copy the ivar and simply returns it, then no need to do anything (well, actually, purists may choose to return [[ivar retain] autorelease]).

