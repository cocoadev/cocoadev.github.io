---
layout: page
title: YearTwentyThirtyEightProblem
---



What are all of the issues related to moving from 32 bit computing to 64 bit computing? I've been using time_t to generate timestamps for logging purposes. While cleaning up some code that timestamps record entries, I started to wonder how things will change when we start to compile in a 64 bit environment. Will     long stay four bytes? If it does, when will time_t be defined as a 64 bit type? Stories about the Y2038 bug often make the comment that this "unix" issue will not be a problem when 64 bit computing becomes main stream. Well the G5 is here, so what are the growing pains we will have to endure? 

--zootbobbalu

----

Unsubstantiated, but as far as I've heard, the only thing that changes size when you compile specifically for the G5 is pointers. This, of course, means that any time you're casting between ints and pointers you're going to need to change int to long long or something like that.

Further clarification would be nice... I wonder if Apple hasn't got something about it on their site.

-- General/RobRix

----

*I wonder if Cocoa suffers from the General/FragileBaseClassProblem?*

My guess is that OS X will have to dynamically bind against data model dependent frameworks. Will this mean that two versions of every framework will have to reside in memory during the transition period? Who knows, since the only clear direction Apple has given so far is OS X will be able to address 2^64 bytes of memory in the near future.

So will there be a pseudo classic mode when applications bind against 32 bit versions of an API? 

Will the alignment be     char (1 byte)     short (2 bytes)     int (4 bytes)     long (8 bytes)?

Are there any rules set in stone?

These are random thoughts without fodder. I plead ignorance on this topic. Apple needs to publish a better roadmap for developers. The smartest thing to do right now is keep a lookout for all logic where     int and     long are assumed to be 4 bytes wide. 

Maybe the clues are already contained in the current version of Cocoa. It would stand to reason that all arguments typed     long are going to be 64 bits wide and all arguments typed     int will stay 32 bits wide. 


----

Just found this very informative article on this topic:

http://unix.org/version2/whatsnew/login_64bit.html

*Summary: a very long (~1300 lines) text about the (not surprisingly) concerns regarding extending sizeof(int) and friends to 64 bits on a 64 bit architecture.* <- wow what a cavalier attitude!!

I have this page saved, so if this link ever breaks just email me and I can post it here (    zootbobbalu at * yahoo * ** dot ** com)
