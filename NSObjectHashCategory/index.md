---
layout: page
title: NSObjectHashCategory
---

[http://www.mulle-kybernetik.com/artikel/Optimization/opti-7.html] has, besides a very interesting discussion on the use of hashing in Cocoa, the following bit of code and advice:

----
    
- (unsigned int) hash
{
   return( ((unsigned int) self >> 4) | 
            (unsigned int) self << (32 - 4));
}


Put it in a category on NSObject, and get a little Cocoa speedup for free! It remains to be seen if this does anything for overall speed. Please let me know.
----

Is this really advisable? Or necessary? How would it work on a G5?

I've inserted it into a project I'm working on with no ill effects, and things seem to be slightly faster, but nothing I couldn't be imagining.

*It will work fine on a G5, which still run in 32-bit mode. If you're asking whether it will still work when compiled using a hypothetical 64-bit mode on a hypothetical 64-bit OS on a G5, which should be coming someday but isn't here yet, the answer is that it will still work. You'll lose the top half of your object's address, but that is probably the least interesting part of the address anyway. It may lose efficiency, but it will at least still work.*

*It may even work just as well as if it hadn't lost the top half of the address.  The link explains that the Foundation code that uses the hash only cares about the last digits of the address anyways, so this should not even lose efficiency.* -FranciscoTolmasky

----

The speedup seems to come from requiring fewer operations once assembled, not from increased uniqueness in hash values. (A summary if you didn't read the link)

- FranciscoTolmasky

