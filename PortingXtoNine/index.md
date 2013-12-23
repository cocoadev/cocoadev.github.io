---
layout: page
title: PortingXtoNine
---

I got started with cocoa a couple of weeks ago and I built my first application in order to replace a very dated app some people use in my office. When I showed it to my boss he said "great, but can we have it run on os9 as well?" (we still have some old machines). I'd appreciate any pointers on the subject (although I have used macs running on os 9-, I haven't developped anything for those systems, I was raised a Win guy, then turned to Linux and adopted Mac when osX came out).

There is not way to run Cocoa on MacOSNine.  It simply did not exist.  All I can suggest is writing your core application logic in C/C++ (which will run on MacOSNine), and then writing the MacOSTen frontend in Cocoa with Objective-C++. --OwenAnderson

*You could also just write the whole thing in Carbon CFM. You'll automagically support both 9 and X without any additional effort. Nothing even vaguely Cocoa-like runs on Mac OS 9 and almost certainly never will.*

If you absolutely *have* to do this, use RealBasic (builds for Mac OS X, 9, Windows and Linux). Otherwise, tell your boss "no, we can't have it run on os9 as well."

But since there is already an older app that runs on classic mac os, then why not make the cocoa version play nice with it, continue to use the old version on the older systems, then once the older machines are replaced, go to cocoa version.

----

Thanks for straightforward negative and for the other ideas. Fortunately I don't "have" to do this, I guess I'll tell my boss to keep it in a drawer till they get some new equipment. As for the suggestions: C or C++, I could, but the whole point of starting this fairly small application was learning how to work with cocoa and ObjC, and then trying to sell the idea to my boss (he actually liked it a lot, he's not to blame for the os9 macs issue and would certainly have osX if he could), re-coding it in a language I know fairly well wouldn't be as fun. RealBasic: heard about it, seen a couple of apps built with it, but I haven't tried it, maybe I'll get into it some day but that will be a different project. Continue using older app... my guess is that will be the only way. They want to change it beacause it doesn't allow people working on several computers to work on the same database simultaneously, my app would do the trick, but there's only one osx computer. The old app was written in something called "revolution" (did I mention some of the macs have been downgraded to mac os9 because we "have" to use that thing mac users used to call hypercard!) and I really don't want to get into that. Last but not least: Carbon... could be. AlejandroAcosta

----

HyperCard should run fine in Classic, shouldn't it? *Depends how much RAM you have.*

"Revolution" is the latest reincarnation of HyperCard (to be brief -- see www.runrev.com for the company's description). So if you have to have a HyperCard environment, what's wrong with Revolution (besides the price tag)? And why do you have to use HyperCard anyway? If your boss won't blow up at you or something, you should mention to him that HyperCard is commercially dead by now. --JediKnil

----

Yes, HyperCard does run in Classic, and it's commercialy dead, and we do have to continue using it for the time being (!). But all of that is out of the question. If I've gotten everything there is to know about porting osX to classic, I guess this discussion should end. But before such an event: does anyone have any pointers on Carbon? (although this could be the wrong place to post such a question) -- AlejandroAcosta

----

But his boss wouldn't get an app if he made it in RealBasic.  He would get a RealBasic program, not an app.

----

Doesn't REALbasic make full applications? I though it did... correct me if i'm wrong. I haven't really tried REALbasic.

*Yes, REALbasic makes stand-alone, fully-functional applications. Dunno what the above poster is referring to. It doesn't make .app PACKAGES, but it does make fully-functional, stand-alone executable apps.*

I think that he was referring to the fact the RealBasic creates gimpy, buggy, non standard ui builds.

----

There's another solution... sort of. It resembles OwenAnderson's suggestion above, and it demonstrates the benefit of strictly adhering to the ModelViewController paradigm:

Compile your model code for MacOSNine using libFoundation instead of the Cocoa FoundationKit. libFoundation is an open-source implementation of the Foundation half of Cocoa, and it's available in source form for download from SourceForge (just include the source files directly in your project). Provided you're not using anything funky like NSMachPort or whatever, it should compile on MacOSNine.

I haven't checked, but I seem to recall that libFoundation is available under something like the MIT license, which is less restrictive that the GPL (you can use it however you like, provided you include a copyright notice in your distribution documentation).

If this works, then you'll only have to rewrite your controller code specifically for MacOSNine (presumably using Carbon).

--ToM

----

Uh, CarbonLib on 9 supports more than enough of CoreFoundation. In fact, an app written to the proper set of Carbon calls can be made to run on 9 and OS X from the same binary.

----

libFoundation certainly does not compile on OS 9.
If you restrict yourself to APIs which are only in CarbonLib, you'll miss out on an awful lot of OS X specific goodness. This means that your app will probably not provide a first class user experience on OS X. Don't do that.
Seriously, OS 9 is about 5 years old now. It's time to move on. -- FinlayDobbie

