---
layout: page
title: VerticalTypesetter
---



Has anyone tried to make a vertical typesetter?

I myself know what I try to achieve (vertical text), but not how to go at it. Any suggestions?

- Anders

----

How's are your efforts working out? I have just started research for this issue and it is a little confusing. When Doing vertical text do you change just the typesetter or do you have to do both the typesetter and the textcontainer objects? If I find out anything I'll post it.

-Andrew

----

Hi! i am working on laying out the text as in circleView, that is in circular manner. In circle view example it draws the glypshs, we canot apply copy,paste operation etc on it. so i want to create a textView which shud layout the text in cicrluar fashion as sooon as i enter the charecter. can any 1 write code for this??? since three days i have been working on this but no effect! please help me...plzzz

----

This is an extremely difficult task to accomplish well. Cocoa text editing simply is not built for it in any way. If you think that three days is excessive for this problem, then you have no idea whatsoever of the magnitude of the problem. If you want somebody to do the work for you, I'm sure you could make a post on General/JobOffer, but be prepared to pay a large sum of money for this, and for it to take a substantial amount of time. Otherwise, set your sights lower, as you're obviously a fairly inexperienced person. Either that, or give up programming and become a plumber, if this is how you react to problems you can't instantly solve.

----
Actually, it's all in how you design your goals.  Since beginning programming, I have been amazed at the consistency with which I have been able to achieve a goal every 1-2 days.  But as this trend continued into its preposterously long run, it began to dawn on me that perhaps I was actually just getting really, really good at subconsciously identifying short, measurable goals and pursuing them.  6 months spent that way, which converts to up to 180 small goals depending on your hardcore multiplier (days * HC  / avdiff = goals accomplished), can really add up.  Even having an app with 60 concrete (albeit micro) features is approaching real utility.
I think your goal is achievable, but the Cocoa text system is very complicated with over a half-dozen interrelated classes involved.  Good luck!

----
Thank u guyz for replying me...actully i have been asigned a project in which i had to layout the text in circular fashion. for me they had given few days for above problem!! so i had no other option than posting ot here! Any wayz if at all any 1 has alredy implemented it..please help me...  i have seen some applications alredy implemented this. eg: disclabel

i thot it wud be easy to implement becoz circleView example was soo  simple....any wayz sory guyz!

----
As a simple solution (no editing) use the coreimagefilter General/CICircularWrap  (page 28 of coreimagefilter reference) Treat the text as an image.  Paulcd

----
Please, please, please try to spell correctly. I know it can be hard, but Safari has a built-in spell checker. I don't know that this problem would have gotten much help in any case, but your chances of getting good help go up dramatically when people can easily read what you write!

----
Sorry again..! i want it to be as text itself. it is very easy in case if i wanted it as image, problem with the image is it wont suport copy,paste operation. 
i have one solution but i dont know how far it works (i am just a beginer!!!). solution is like this
Handle the key event so that as you type it should draw charecters.
and then provide cut,copy, formating manually

----

OP here; I originally posted this thread to watch in dismay as it dropped 3-4 weeks down through the list of current discussions. Apparently this is a highly complex issue that several people would like to see a proper solution to. For this reason I have rejuvenated my interest in the issue; and I feel a wish for as to create an Open Source variety Vertical Typesetter for Cocoa (and General/GNUstep?). If anyone else is interested; please make sure to drop me a line at williham [dot] totland [at] student [dot] umb [dot] no (long email address. o.O)

- Anders

----
Anders.. what is the status of your work? Even i searched a lot in the net for te things related to text layouts. but i dint get it.

----
I've got half a mind to give up. The only application I've found looks to be an ugly hack of off General/TextEdit with some not very smooth solution using x<->y coordinate substituition (flipping an General/NSTextView sideways) and some glyph haquery. It seems as this kind of functionality has limited appeal, but it surprises me that Mac OS X ships with some pretty nice semi-high end KCJ fonts and no vertical writing mechanism, even in iWork. Even M$ flippin' office has that.
I'm still interested in opening up an open source project, but as it stands I don't know where to begin.

----
Probably you are working only on vertical typesetting.. i have seen a nice application which lays out the text in almost all direction. application is disc label. but it is not open source. have any one found open source project of this kind?

----

Time to bring this back from the depths? OP here, problem still exists AFAIK, or maybe I've just missed some great developement on the front. Regardlessly, still not a big turn-out for the idea? It seems to me as if it _should_ be an easy task, creating a vertical typestter subclass of General/NSTypesetter and substituting it for the real deal. But maybe I've missed something?

- Anders Williham
