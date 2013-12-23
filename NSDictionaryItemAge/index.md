---
layout: page
title: NSDictionaryItemAge
---

Is there someway of figuring out which items stored by an NSMutableDicionary instance were added ahead of others?  For isntance, were this an array, and we hadn't done any iserts of replaceObjectAtIndex:withObjectAtIndex: moves, the indices would store the "date".  Is there some way of recovering the same information from an NSMutableDictionary instance?
What I really want to do here is this: set a limit on the number of objects that can be added to an NSMutableDictionary object (let's say 100).  When I add the 101st object, the 1st drops out, and so on.

----

I believe there's something very similar to what you're looking for in the OmniFoundation framework, called OFDatedMutableDictionary IIRC.  -- Bo

----

ummmm... 
i can think of 3 solutions.
1. use the insertion order as the key for the objects in your dictionary, so that you can eeasily figure out which order objects were added.

2. instance vars in the objects that are added to your dictionary, that you set to the insertion value as you add them to the dictionary, not as easy but offers mire flexibility in the long run.

3. use a mutable array, which is built to keep track of the order of objects.

