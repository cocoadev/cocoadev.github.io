---
layout: page
title: WhenCanWeStartDiscussingTheNewStuffFromWWDC
---

I want to hear about the new API additions!  when is it legal to start discussing that?

Right now. Just don't put your name on it.

----

If you haven't signed any NDA, then I guess you can discuss all you want! :-)

----

Let me start the ball: anyone seen any improvements to the spell checker on how to programmatically set the language? many programs know which language the user is currently writing in, so it's stupid to have the user make the change, especially as most of Europe is bilingual and write half our texts in one language, and the other half in another (causing quite a lot of switching back and forth) -- but then again, despite Apple claiming to support overseas users (with only 54% being US citizens), there are many aspects of Mac OS X which reveal that they don't care much about us, such as having the calendar in Data & Time settings show sunday as first day of the week (which I think was also the only option in the first versions of iCal, and still the only layout option).

----

What about the inability to unload a shared lib opened via dlopen() ? I used to be able to do this under linux, and while it's not critical for my work, it would make my life a hell of a lot easier -- since I could simply have my program reload a freshly tweaked and recompiled library, instead of shutting down and restarting every time. -- Anonymous

Mac OS X's dyld currently can't unload dynamic libraries. -- General/AdamAtlas

I thought dyld could unload dynamic libraries, and it's the Obj-C run-time that causes the bundles to be un-unloadable.

The objective-c runtime doesn't seem to have anything to do with it. I wrote a C test program a while back and, not only does it *not* unload, but dlerror() doesn't tell you anything. It's annoying. -- Same Anonymous

----

what about this new General/NSController class??

----

General/MacSlash reports (http://macslash.org/article.pl?sid=03/07/15/1054245) that General/NSAttributedString can handle Word's *.doc* format. Just the basics, mind�font, size, tabs, etc. But there are new text and paragraph attributes, including, apparently, the outline font style. -- General/DustinVoss

----

Really? But that doesn't make any sense to me...

----

Any chance apple will fix dyld to let it unload libraries? It seems to me like basic functionality for *any* OS. Windows does it, General/BeOS did it, linux does it. -- General/ShamylZakariya
