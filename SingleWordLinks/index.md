---
layout: page
title: SingleWordLinks
---



More and more often, I've been running into the difficulty of coming up with creative ways to get single words into links (think "AnObject", and then thing how we'd go about making a link for Protocols). While I think that the WordsJammedTogether route is truly excellent, I, and others as well, I think, would appreciate one of two things:

1) something like ** %%BEGINLINK%% **and** %%ENDLINK%% ** around a word which would then become a new page - you could use ** %%BEGINLINK%% **Protocol** %%ENDLINK%% ** to make a page called Protocol (obviously)

2) as an interim solution (and likely a much easier one), extend the WordsJammedTogether thing to not only include NS-prefixed words, but any assortment of capital letters. So, you could do AProtocol, or AMessage, et cetera. It wouldn't be as perfect as option #1, but it would work for now.

To restate what I've stated many times: I don't know how Wiki works, so I don't know about feasibility.

But it's something to think about, anyway!

Thanks very much again, Steven!

-- RobRix

----

A link wrapper for single word links is a good idea.  I'll have to see if it's do-able without too much fuss.

-- StevenFrank

----

Consider the possibility of using brackets [ ] around a word to make it a single link. This is how everthing2 works, and it's fairly convenient to link something that way.

-- AaronSittig

----

Thanks, Steven!

-- RobRix

----

Beautiful job with the timestamps!

BTW, you on the North American west coast? Pacific Time, that is. Just wondering, because the timestamps are three hours back of my clock, but they serve their purpose perfectly, so I'm not complaining :)

-- RobRix

----

Yep...  Portland, OR.  :)

-- StevenFrank

----

That's a far cry from Port Elgin, Ontario, eh?

The net sure is cool!

-- RobRix

----

I actually have a couple of recommendations: 
*Choose a better pattern match for WordsJammedTogether. Perhaps ([A-Z][a-z]*****)+, allowing ACategory or ObjC or KTMatrix but not words with just one capital letter.
*Disable mismatch flagging in CODE blocks, as this is invariably annoying it seems, but without disabling the linking system, so defined words get links but undefined ones stay nice and inert.


I know *exactly* how easy this would be to implement. (Namely: the first easy, the second hard.)

I also recommend a shortened form of the italic/bold commands, namely %%B%%, %%/B%%, %%I%% and %%/I%%. maybe \\%\\%C\\%\\% and \\%\\%/C\\%\\% for code, too. These would obviously coexist with the existing ones.

1 July 2002 - Most of my suggestions are now in CocoaDev's engine. I've retired this discussion.

-- KritTer

