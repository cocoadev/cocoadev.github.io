---
layout: page
title: MutableArraySortUsingSelector
---

Hey there, guys.  I have an NSMutableArray with several NSString in it, and I simply want to sort by alphabetical order.  I can't figure out how to do this though... can someone give me an example?  I'm guessing the sortUsingSelector: will be the best way to do it, but I don't understand how I would implement this sort of thing.  Thanks for any and all help.

--CharlieMiller

----

If all you want is alphabetical order, then use the selector **compare:** or **caseInsensitiveCompare:** as the selector. This selector is implemented by NSString. How **sortUsingSelector:** works is, it will pick an object from the array and send the selector to it, with an argument of another object. The selector has to return NSOrderedAscending/Descending/Same. **sortUsingSelector:** uses this to sort the array. **compare:** and **caseInsensitiveCompare:** follow this convention, so they'll do the trick just fine. -- DustinVoss

