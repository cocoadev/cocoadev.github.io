---
layout: page
title: NSEnumeratorLastObject
---

It seems that [myNSEnumerator nextObject] will object retain] autorelease] the last object it returns.  Does anybody know why the heck it's doing that?

----

    [[object retain] autorelease]; is the Apple-recommended method for writing accessor methods to ensure thread safety.  By doing that, you 'guarantee' that the object will not be deallocated while the calling method is still playing around with it, even if it's released in another thread.  My guess is that the enumerator actually does this for all objects it returns, not just the last one. -- Bo

----
I know that the retain autorelease couple is recommended -- but the mystery lies in the fact that this appears to happen only on the last object -- the other enumerated objects do not show up in the autorelease pool AFTER the enumeration ends.  It is possible that they are entered and the removed when nextObject is called... which might explain why the last one doesn't get taken back off the autorelease pool... it's very odd and irregular though to have side effects on only one object in a collection.

----

I just ran a little test on it and you're entirely correct.  What seems to be happening is that the enumerator (an [[NSArray's -objectEnumerator in  my case) is retaining the whole array while it's still got objects left to retrieve (which should keep all members from being deallocated), but once you grab the last object, it releases the array and just retains and autoreleases the last object.  This actually seems like a pretty clever way to maintain thread safety while avoiding cases where the autorelease pool would become clogged.  -- Bo

