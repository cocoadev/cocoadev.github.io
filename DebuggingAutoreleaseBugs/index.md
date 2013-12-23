---
layout: page
title: DebuggingAutoreleaseBugs
---

I've got what is obviously an autorelease bug (as in, I'm releasing an object too many times).  A few seconds after I perform a certain action (which is complicated, and involves several client-server messages, and quite a few objects created), I get an "EXC_BAD_ACCESS" signal, and my stack trace shows that I'm in:

    
objc_msgSend_rtp
NSPopAutoreleasePool
-[NSApplication run]
NSApplicationMain
main


So I'm pretty sure that it's an autorelease problem. How do I go about debugging?  In the past, I've always just looked through my code carefully and counted retains and releases, but there's enough code in here that it's pretty daunting.  Are there any good methods of debugging this?

----

See the DebuggingAutorelease page. And do try to perform a search before you post; if you have a problem, it's likely that somebody else has had the same one before.

----
If you do a search (click on the search link at the top of every page) and do the google search for "debug autorelease", the DebuggingAutorelease page isn't on the first page of google results.  I did in fact do just this search, and found no relevant results.

----
Somehow it's always the case that one Googles for hours trying to solve a problem, only to have someone else find it in a nanosecond with a slightly different phrasing. Happens to everyone. It's always easier to find something you know is there. For future reference, checking out the CocoaGlossary, linked to fairly prominently on the front page as well as in the quick links on each page, can often get you where you want to go. -- KritTer

----

Perhaps this is a good argument for better site indexing. 'debug' is the "lowest common denominator" keyword for the search being done, and a good search engine should pick that up in 'debugging', 'debugged', 'debugger'. It's tedious to have to search each those terms when 'debug' should do, and can become futile if you need to use multiple keywords to try to narrow the results down (too many results often is as useless as too few).

----
The best site-search is ultimately to ask a question, because you will almost surely miss things otherwise (like stuff that people haven't written down yet). -- KritTer

----
The more reason *not* to use Google :) They want to 'index the world', but they can't index information, just static words. 'house'=='home'=='dwelling' in concept, but they'll only index 'house'. -- Casual Reader

*well, yes, it's your job as a wet and fuzzy intelligence to recognize that you might need to be more flexible than Google*

----
If Google, with its in-house stock of Ph Ds, can't get their index to do that, *I'm* not going to try. You're welcome to code such a search engine, though. I'm sure StevenFrank would be happy to add it to the CocoaDev site once it's done. -- KritTer


----
I'm not suggesting you write a whole new search engine, there's actually OSS ones available (see http://dmoz.org/Computers/Open_Source/Search_Engines/ for example), and I'm sure they're far better suited for this than google's "search engine", which is nothing more than a link popularity ranker with indexed static words. -- Casual Reader

----

*(Editor's note: This discussion evolved into a round-table on searching and indexing strategies (as in using CocoaDev search and Google to find the answers to questions posed here). It never really DOES get into any concrete debugging discussion (which is copiously represented on other pages anyway). I have it in mind to move this to a page that discusses HowToWriteHelpfulAnswers (AdvisingCocoaNewbies might also be appropriate, even though the OP on this one was more advanced.) Perhaps under a sub-heading that suggests patient search strategies. It's pretty hopeless that ANY single page could topically deal with DebuggingAutoreleaseBugs, because they are almost always somewhat cryptic when they first manifest. This may point to the futility of even searching for previously sketched-out debugging solutions, which are all over the place, after all. And MemoryManagement is better taught bottom-up than top-down. This investigation was inspired by the solution of HelpMeStopMyApplicationFromCrashing, one of several dozen idiosyncratic DebuggingAutoreleaseBugs on this site. THIS page might be good with a Topic tag on it and a collection of autorelease gotchas linked to it.*

