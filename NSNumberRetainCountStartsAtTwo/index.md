---
layout: page
title: NSNumberRetainCountStartsAtTwo
---

Because NSNumbers are *immutable* (constant values), NSNumber caches instances. Thus, the first time you create, for example,     [[NSNumber alloc] initWithInt:5], it has a RetainCount of 2. The next time you create a number with the value 5, NSNumber will retain the same object from the previous invocation and return that from     initWithInt: � there's still one NSNumber object, now with a RetainCount of 3. Whether this is done for MemoryOptimization or just so they can compare numbers with the equality operator, I'm not sure.

*Note: This was a MailingListMode question where the author requested he be e-mailed when somebody answered his query. I just replaced it with an explanation of the phenomenon he was asking about. Feel free to e-mail him and/or delete this topic if it's not of interest to the CocoaDev body at large.*

**Just a postscript to this fiasco: The guy who started this asked to be contacted via gmail, but he neglected to specify within which gmail domain (there are several) he could be reached. This is just about as narcissistic as I have ever seen things get with this kind of thing.**

----

Does anyone think this is related to the discussion at CFNumberMemoryLeakProblem? If it is, then the two pages could be consolidated...

