---
layout: page
title: GrabbingOnlineInfo
---

Three questions:

*1) Is it legal to fetch information from online sources such as dictionary.com or google.com without their permission (i.e. you tell your webrowser the search and it fetches the info)? I'm pretty sure its legal, an iPod program implements something like that from weather.com. 
*2) If no, how would it become possible/legal?
*3) What type of code is involved?

----

Using someone's copyrighted material without their permission is obviously illegal. If it is not copyrighted, by all means access it. 

If you are not wanting to access search engines and want something like stock prices or other related things, use SOAP calls to get the information.

-- MatPeterson

----

Usually commercial sites have a policy that they don't allow screen readers.  This hurts their add revenue and band width.  Try to stick to noncommercial / government sites.  For example, my forecast script uses Environment Canada which specifically allows this sort of thing.  Another one of my apps uses amazon.com's XML API.  Both google and amazon offer this sort of thing though you'll need a "developer token" (free).
* amazon's dev kit - http://associates.amazon.com/exec/panama/associates/ntg/browse/-/1067662/102-3408071-2874513
* google's dev kit - http://www.google.com/apis/
-- SaileshAgrawal

