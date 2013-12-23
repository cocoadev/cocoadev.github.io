---
layout: page
title: HowOftenShouldWeReleaseNewVersions
---

I always wondered, what should be the average time frame for releasing new versions of our software? I'm talking about shareware apps, not huge commercial apps. Because some people release updates all the time, very frequently. But is there any standard or rule that should be followed? --KevinWojniak
----

I'd say that a rule of thumb would be, release bug fixes often: if people find a bug, they'll look for a fix, and if they don't, they won't bother with the update. As for releases for feature improvements... as quickly as you have them solid, I guess.

----

My rule is....as above. :) If there is a bug, fix it immediately. If I am making features, I usually pack in a lot of them so that the user does not get frustrated by having to upgrade every minute.

----

Release bugfixes often, but not TOO often. I'd say if you have a bugfix to release, and you've done another bug fix release in the past week or so, sit on it for another week. Often there's another bug lurking just around the corner anyway. As for new features, my rule of thumb is never to allow a new feature into a release until after at least a month of beta testing.

----

This depends entirely on your product / market segment. I still think it's useful to look at commercial software and see what considerations they have, as there are some lessons to be learned even for sharweare authors.

Microsoft releases OS'es on a huge multi-year basis. Why? Consider all the consulting businesses, IT infrastructure, and other services that need to be constructed around these beached whales. You can't bring that up over night -- and if you could, your customers would freak. 

Apple releases OS'es about once a year. Why? Mac OS has never required as much support infrastructure as Windows, so it's a simpler matter to rev training materials or whatnot. But Apple's not going to be able to hit up its user base for $130 bucks more frequently than once a year or so.

Apple releases OS patches generally every 4-6 weeks, if necessary. Again, a certain amount of infrastructure is required here, so releasing more frequently will create thrash. Also each bug fix introduces the opportunity to cause a new bug -- and bugs reported "in the field" are often hard to isolate.

Apple releases OS security patches immediately. I think we've even seen two in as many weeks. The reasons are obvious.

Commercial app vendors tend to release new versions every 6 - 18 months. These releases are occasionally targeted to new OS'es (i.e., "Upgrade to 1.5 -- optimized for Panther"), industry trade shows (MacWorld, N.A.B., Seybold, etc.), or times of year (Christmas, Back-To-School). Updates are released irregularly -- as needed. Consider featureset / cost ratios. Smaller / cheaper apps might rev twice a year, while professional graphics / video software might roll out huge, expensive updates less frequently.

Successful shareware / freeware authors release upgrades / fixes somewhat more frequently than other software sources. Generally they update the apps when they have the time or interest to work on them. Given that downloading software on the internet (read: shareware / freeware, not warez) are generally following current releases on sites like www.versiontracker.com, such software probably can't be rev'ved "too often", as long as the updates are stable and reasonably priced. Again, releasing fixes won't do your customers any good if you're constantly introducing new bugs. 

There are standards or rules for specifically when to release something, especially once you're talking about commercial software. You can trend how often new bugs are found vs how frequently bugs are fixed and use that as a helpful metric. Using quality as a metric is much better than using calendar time; having a 30-day beta period for your new feature is useless if no one is testing it, for example. But if you're not actually formally tracking bugs / fixes that technique is not going to help you. Also, there are various standards and rules for describing the release process itself (alpha = major features complete; beta = feature complete / UI freeze; final candidate = code complete) which is generally worth understanding. You won't be able to ship your product if you're always just adding "one more feature".

-- MikeTrent

