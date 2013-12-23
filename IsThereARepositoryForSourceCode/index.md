---
layout: page
title: IsThereARepositoryForSourceCode
---



I have (with the help of some CocoaDev folks) finished a simple but potentially useful program. Is there a site where one can post source code in case someone else wanted to use it? It's not important / sophisticated enough for sourceForge, I think, but if there's a simple post-what-you-wrote site, it might fit there. If you know of such a place, please direct me! Thanks. -dan

----

I intend to do the same when I finish my latest project.  seeing examples of code makes such a difference! 

----

What is important is not just the source code but analysis or narrative of the context and just *how* you developed the working code...Most flat source code examples are pretty useless once they have grown to several hundred or so lines, without such an an analysis. That said, you can easily throw together a website with a link to the source code archive... Of course, any of the free hosting alternatives bombard visitors with popups (not such a problem for Safari users etc.)

Consider also whether what you call "potentially useful" code is that which you have developed for a particular aim, or as an exercise to demonstrate a particular feature of the API (like Apple's "sample code" examples). The former by and large tend to be less useful to others than the latter.

For example, a document-based app that implements an NSTableView and a toolbar (but nothing else) is a type-example (of course this has already been done, e.g. at O'Reilly's MacDevCenter, but you get the idea...)

Extending (and, sometimes, cleaning up) such examples is always a useful contribution.

But as for a repository for such examples, it still seems to be up to the creator to make the website and publicize it.
----
Right - I'm just wondering if anyone knows of someone who has set up such a site.... Admittedly there could be no one who would care to read or use the code, but it's relatively well commented, and it can respond to standard serial-device via USB input to trigger QuickTime movie(s) based on the input. Either way, I'm just trying to give folks the option to look at it if they wish.... Thanks again! -dan

----

never underestimate what could be useful for someone.  it would be great if you posted the project somewhere and started a thread here on cocoadev describing it.  That way when people run a search they may come across it.  If you are in need of webspace I can host it for you.  EcumeDesJours

----
OK, it's rough, but it's a start. I've added a Mac OS X link to the navigation bar of my Web site, http://www.ascendiac.com . The source is Stuffed, with a small blurb describing the current use & purpose of the program. I'll try to make the Web page more generalized so I can post links to other people's sites. Right now, I'm using Yahoo! as my host, so I can't personally collect files, but if anyone wants to advertise a link to source code, I will try to maintain a list. Just click on the feedback section & send me the URL. Let's see who's willing to post their OS X code.... Thanks again for making the Mac a great experience! -dan

----

I've always thought of CocoaDev itself as more or less a CodeRepository - i.e. a place to say 'Here's what I've done and how I've done it. Does anyone have a better way?' Not a place to say 'How do I do [x]?' This site is far more useful (IMO) when used in the former way, rather than a sort of instant-gratification mailing list.

----

I have managed to write a functional program in Cocoa by looking at source code and reading on the web. Seeing real code in action has been the most helpful. I know that it works and that it is the right way to do something, and I was given an insight into how to do some things more efficiently. I think a site that people could upload functions and examples would be really beneficial to people starting out, and existing developers. Applescript have a great site in  http://macscripter.net/exchange/  I found it very useful and gave back to it when I was writing Applescript. -- Sky

----

I certainly agree -- the more open source out there the better. The point is not as much reading the entire source of a program to learn how to program, but either look at particular features, like recently with the SafarisTabBarView where Adium has a cool implementation for that (and is OpenSource), or simply to improve on existing programs rather than re-invent the wheel.

I was considering creating a SourceForge clone geared toward open source Mac OS X or perhaps just Cocoa projects -- partially just to get some experience writing an "advanced" web application and partially because I was lacking such a service for my own projects (they'd probably drown on SourceForge, plus I'm not to fond of their design, and if I were to anyway just host my own projects with anonymous CVS etc., I can just as well make a service out of it).

Comments would be welcome! The goal is to try and centralize open source OS X projects rather than fragment, so if there already is a service for this other than SourceForge, or you think SourceForge should be used, speak now! :)

--AllanOdgaard

That would be great! particularly if the repository includes frameworks, or even single classes, so one can go shopping for parts, all with a good search engine... --CharlesParnot

I think we need a decent source respository. How about, in addition to a website, an opensource cocoa app is built? Quick searching of source, code comments, syntax highlighting, drag and drop to xcode, grouping, 'sumbit your classes/methods', etc...  I'd certainly be willing to join a team interested in building such a repository.
-- Luke.

----

Not a repository, but there's the newish http://koders.com/ source code search engine. As of today (2004-12-02) ObjC is not one of the languages they index -- but I imagine they could be convinced if community interest were evident. They do index Python source, which includes some PyObjC stuff.

----

Perhaps a little bit off topic, but the creators of CocoaDevCentral are currently working on a forum for the community. I think this is a great thing as we currently lack any real, centralized forum (I'm sure they're others in existence, but cocoadevcentral is already pretty well known). 

Now everyone go write them an email of support!

----

So any update on whether someone is working on some type of Cocoa code respository?

----

So what would folks really be interested in?  A cocoa-oriented sourceforge like thing where people can keep complete applications, or a CPAN-like library of re-usable, relatively small modules?  Would folks be willing to offer support (code, maintenance, moderation, money?) to such an endeavor?

----

I think both would be useful: have a section for examples and a section for modules. I'd definitely be willing to code, maintain, and help pay for such a site. Unfortunately, I'm a bit busy with readying Pixen 3 for launch right now... but if no one's started such a site by the time I get back, I'll get right to work on one. I need an excuse to practice Ruby on Rails. :)

As for hosting, I have a couple options I can provide, neither of which is really ideal: I run a server from my house for my personal domain and a couple other smaller sites, but it only has 768 kbps up and perhaps less-than-ideal uptime. On the bright side of this, I can install with impunity whatever modules we feel like using, and there's not really a disk space / bandwidth limit that would get in the way. Besides this, I do have a free hosting contract that I use for Open Sword's site. They've got *tons* of bandwidth and really good uptime, but I'm limited in the way of disk space (a gig, I think), and we probably couldn't use Ruby on Rails or SVN. Anyone else have hosting they could contribute? I'm sure I could scrape something together from someone I know if not.

I'm envisioning a system wherein developers tag submitted code, the site coagulates things with the same tag (sorta folksonomous). And then the individual code pages would be wiki-ish in that anyone could edit the code for improvements / clarifications / explanations / refactoring / optimizations. There would be a revision tracker (heck, maybe it'd integrate with a monster SVN repository! That would be *sweet*) as people made their edits.And then maybe the site would have a forum for normal Cocoa discussion and where each module / example had a thread for commenting (or maybe just straight-up wiki-style discussions).

I'm thinking that given the sharing-oriented nature of the site, perhaps only non-viral licenses should be allowed (or at least strongly encouraged). Submitted examples / modules would be able to choose a license, and then any modifications others made would have to conform to it. Such a site shouldn't be very hard to code, really (especially with RoR): it's mostly C.R.U.D. I'd think that at least at the beginning, moderation would be required for submissions / edits, but perhaps as the site matures and a large user base grows, it can self-moderate as CocoaDev does. I'm really excited about this project, and I can't wait to work on it. -- AndyMatuschak

----

Alright, many months later, I've finally started to design / code this thing. But! I have serious trouble working on something that doesn't have a name. I thought for a while about what it should be; a lot of things were taken, but between me and a bunch of AIM correspondence, I have two candidates:

1. Curly Bracket

2. Bracket Fungus

Both imply a bit of folksonomy (because a bracket is a hash in many languages), collection / community, code, and a bit of irreverence. The first also has the added advantage of being extremely easy to abbreviate (maybe "{}"). Both lend themselves very easily to logos. Thoughts, anyone, or additional suggestions? It'd be nice to have a name integrated into the TLD like del.icio.us or giantcyb.org, but I'm not sure that's possible. -- AndyMatuschak

----
how about just 'brackets'?  reminiscant of 'sprockets' :-P  EcumeDesJours

----
bra.cket-fung.us ?

----
I may be in the minority here, but I find "clever" domain names that spell out a "clever" word once you squint away all of the periods to be silly and somewhat annoying. What's wrong with bracket-fungus.org?

----

How about just C<nowiki/>ocoaForge?  A bit like R<nowiki/>ubyForge, I suppose, and it's instantly recognisable from the name what it is. -- J<nowiki/>amesGranby

Oh. Yes, that works fine, except that I was planning on making it for all languages, not just Cocoa. -- AndyMatuschak

----
Cocoa is not a language. And putting "bracket" in the name makes it sound like it's going to be restricted to C-alikes, since most non-C-like languages don't use brackets, at least not all over the place like C does.

----
Ouch, burn. :) Even the non-C languages usually use brackets arrays or hash declarations. I mean there are some (like Io) that don't, but on the whole, they're pretty universal. -- AndyMatuschak

----
Well, it's just that when I think of "bracket" in the context of programming, I immediately think of the curly version, which then makes me immediately think of C.

I don't have a ton of non-C-like experience (and by C-like, I mean not only C/C++/ObjC, but also Java, C#, and similar), but it seems that brackets (curly or no) are much less common. Ok, perl has them all over the place just like C, but Python doesn't use curly brackets at all (right?) and the square kind are somewhat rare. Other languages I'm familiar with (ML, Lisp, Prolog, Other?) don't use them to the extent that I identify the word with them.

Enough destructive criticism, how about something constructive. Since it's a source repository, you could just call it Source...For... never mind. I think basing on punctuation is going to put the emphasis on one language family no matter what (gee, I wonder what ParenthesisForge is for...), but maybe it's still the best way to go. Are brackets the best punctuation for the job? If you don't mind an OO bias, you could call it ObjectForge or Object Fungus or something. Beyond objects, I can't think of a catchy concept that's common to lots of languages. Function World? Or how about the Stack Heap? Hopefully this provides some food for thought, even though I despair at its being useful directly.

//I second cocoaforge.org

----
Cocoaforge would be sexy. I'd like to do some work on it... making it look good and what not, with my amazing web design skills... 
-- JohnDevor

----
But a community for all programmers, not just Cocoa ones, would be much more expansive. -- AndyMatuschak

----
General 'programmer communities' are a dime a dozen and I never spend any time there; I look for communities that focus on the language I'm using (and seeking help with / resources for), not 'programming in general'. This is CocoaDev.com - we come here as Cocoa developers, so that's why we're talking about "cocoaforge". *- Amended by OP for clarity.*

----
Exactly, a more specialized community will be more helpful.  Source code repositories for other languages already exist, but I don't think that there is one (or at least a good one) for code which focuses on Cocoa.  If it is a repository for any type of code, all it will be is another P<nowiki/>lanetSourceCode-type site.  Also, would your proposed any-language site focus on programming for Mac OS X, or just any platform? -- J<nowiki/>amesGranby

----
A repository for **Mac OS X targeted Cocoa source** sounds like a fantastic idea to me. The snippets and tutorials on CocoaDev have been an important tool for me in learning ObjC and Cocoa as have the responses to my and other people's queries from the people on this site. I envision a sibling site to CocoaDev created in collaboration with the people responsible for this site. Your thoughts on this?

What I have to offer I offer as my gift back to the community. 

I have a very inactive sideline developing and hosting sites. The company I use provides me with 5Gb of storage and 50Gb of bandwidth. I started my little business to provide a service to a couple of local non-profits. I expanded slightly to a point where everything is just about paying for itself. Cost neutrality has always been my goal. The sites that I am currently hosting are using about 100Mb of my storage and are very low traffic. I can set up a Linux hosted apache server with PHP4, Perl5, Python, CGI, fastCGI, MySQL, etc. I can also set up a Windows based environment but I would argue against that. I am reasonable proficient in standards based web development using PHP, HTML and CSS. I don't know if I would be willing to take a lot of time away from my current Cocoa project but I would be willing to spend a little time to help with the actual development of a site as described in my opening sentence.

I'll leave at that for now. Many details would need to be ironed out. As long as there is a real interest in making this happen and some people that are prepared to put in the many hours that will be required to build and maintain it then I'll set up the start up host and get the domain name registered. Anyone serious about this should continue to respond to this thread. You can contact me directly at 

developer <circle with the a in the middle> dragonkin <structure member accessor> ca

JeanMarcLachaine

----

Yeah, I've actually started a RoR project for it; I have some user CRUD / management stuff in place already. I'm also slowly being convinced of the advantages of having it be Cocoa-only over universal. Anyone else want to speak out on that front? If anybody wants to talk design or anything, my email address is andy@andymatuschak.org; I can also be reached by AIM handle of xhaosessence. -- AndyMatuschak

----
Try here http://cocoa-library.luinithil.com/ it's a wiki kind of code repository. I have written it for fun using CakePHP. But be careful it's still in beta. I need some people who can post some code. So any volunteers are welcome... -- SimonAndreasMenke
----
I've been away (i.e.: not programming) for a while, but I started a new Cocoa project recently and received help (as usual) from the CocoaDev collective brain. Glad to see you put up a page with a source code repository after all. I've added my $0.02 worth of source, now that it's functional! (App is "Billing Project"). Thanks for keeping up an outstanding site. -dan (Wambold) (Note: someone used the alternative spelling, "speciali(s)ed," above, and the Web page rejected the edit because it found the letters "ciali(s)" inside. I have changed the spelling to include a 'z' (a 'zed' to those across the Pond) so that I can post this edit. I even had to use parentheses just to write the offending word so that this note would be accepted....)

