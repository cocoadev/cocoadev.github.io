---
layout: page
title: SafariAndCocoa
---

(This page is hopelessly outdated. Does anyone mind if we cull out the obsolete -- and heavily opinionated -- discussion? Perhaps this should become one of the RetiredDiscussions ...)
----

I've heard Safari is a Cocoa app. Does anyone know how Cocoa developers can take advantage of this fact?

-- zootbobbalu

I read that Apple is going to release  WebCore and JavaScriptCore frameworks under some open source license.  I imagine that we'll be
able to use these to render HTML in apps.

--dtauzell



WebCore and JavaScriptCore are released: http://developer.apple.com/darwin/projects/webcore/

They build on KHTML, KJS and PCRE which are all GNU LGPL'ed and Apple is taking advantage of that by not releasing Safari as open source, just their changes to these libraries. 

Apple is using the open source community for their own benefit, without giving very much back. They have broken the hacker ethic (share everything) by taking and just giving back half.

TheoHultberg/Iconara

--- I wonder why OmniWeb wasn't included in the speed comparisons of Safari versus others???

At least they're giving back *something*. I don't really care if they've broken the hacker ethic or not. Anyhow, with the size of the app and all... probably WebCore/JavaScriptCore *is* the bulk of the program. Most of the rest seems to be elmer's glue and thumbtacks.

Apple gave back exactly what they took.  The code for an Objective-C++ reimplementation of Konqueror would not be very useful to a Linux project.  They have, however, given back all their modifications to the rendering engine (which is cross-platform), and their reimplementation of some Qt and KDE functions. --Owen Anderson


----

Phil Schiller says that there will be some kind of API for the KHTML rendering engine. I wonder if that means there might be an NSSafariView in the near future :-) 

Making a custom safari with tabs would be a piece of cake!!

--zootbobbalu

----

**From the newest ADC News e-mail (#332):

"In addition to providing the best web browser for Mac users, one of the goals of Safari is to provide a fast and efficient HTML rendering engine for Mac application developers. Apple is actively preparing a Safari SDK that will be available later this year."

I'm happy.**

---- 

YES!! Apple has posted the SDK for Safari.

http://developer.apple.com/darwin/projects/webcore/index.html

-- zootbobbalu

----

They might have posted the SDK, but it really doesn't do anybody much good. There is no documentation for the frameworks, AND I have heard from the cocoa-dev mailing list that people who * have * tried to use the framework in their applications have found that you have to subclass obscure classes and override methods that you shouldn't have to deal with (lower-level methods that are usually taken care of for you, like in the finished frameworks). Also remember that the 2 frameworks are still major works in progress, and we have no idea when they will become finalizaed (complete with documentation). So yeah, it's cool that Apple is finally giving us something better than the HTMLRendering framework, but let's just get too excited just yet. After all, you still can't really ** use ** the frameworks as of now (12 Jan 03). Sorry to be such a pessimist, but it really irks me when I spend the time downloading and compiling these two frameworks, only to have them changed the next day. Not to say that is a BAD thing because it means they are maturing, but it also means that they are in no way ready for a third-party developer to use in a production application.

-- MarcWeil

----

Quote (above):
"I wodner why OmniWeb wasn't included in the speed comparisons of Safari versus others???"

Because OmniWeb's current renderer (OmniHTML) is so slow, Steve Jobs wouldn't like to insult his beloved Cocoa by having OW, a Cocoa-written app, be shown "off the charts" even past IE.

Oh, and has anybody found out how to do anything useful at all with WebCore yet? Someone on the cocoa-dev mailing list figured out how to paint a page full of red in an NSView (though that was not specified in the input HTML page) and write error messages about missing renderers in the console. Useful!

-- AdamAtlas

----

**Although I've now deleted the ADC News e-mail, since they were announcing WebCore and JavaScriptCore in it, I got the impression that the Safari SDK they were talking about was something else altogether. It sounded like it would be something that you could actually, you know, use.**

----

The case is closed for now. I got a reply from David Hyatt (member of the Safari team, and author of Chimera), a two sentence reply that explains everything:

"You don't use WebCore directly. An embedding API is going to be forthcoming (WebKit), and that's what you will use."

That confirms some of our suggestions, that WebCore isn't to be used directly. And we clever hackers have already run ClassDump on the WebKit framework found in the Safari bundle (see the WebKit page; the listing of classes is there). I don't know about the rest of you, but I'm impatient to use WebKit :-) . So I'm trying to put some sample code together from what we know about WebKit, and when (or if) I get it to work, I'll post it here.

-- AdamAtlas

----

Follow-up: I gave up. WebKit is too confusing. I'm just going to wait until there's documentation and public headers. David Hyatt has confirmed that WebKit will eventually be public- I'm guessing around the same time Safari itself is released as non-beta (when that will be I don't know).

Of course, if anyone else is impatient and up to the challenge, I'd be happy to ... um ... let you do the work.

-- AdamAtlas

I'll take up the challenge.  Did you make any progress you can leave to me? --OwenAnderson

----

Apparently the Safari SDK (WebKit et al) will be unveiled at WWDC. -- FinlayDobbie

----

It seems the Omni Group has managed to wrap WebCore themselves - OmniWeb 4.5 uses it . -- Anonymous

Yeah, I emailed Dave Hyatt (the Safari developer who works on WebCore/rendering stuff) about that, since Apple told everybody that WebCore itself isn't useful in its current state. Dave said he doesn't know how they did it, and it must have taken considerable effort to get it to work in their system. He said that TheOmniGroup is basically being impatient. -- AdamAtlas

Then again, they're now competing with yet another piece of freeware, one from the system vendor, which looks to be quite a good product. If they want to continue eating, impatience isn't exactly a bad idea for them. I'm not gonna comment on Apple's actions beyond that, but I *will* say that when OmniWeb 4.5 or even 5 is out, I'm probably going to buy it. Here's to increasing business for TheOmniGroup! -- RobRix


----

Does anyone know if a Public Beta 3 will be released soon, so that Safari will start rendering pages correctly the first time?

----

Beta 1 worked just fine. I have had problems with 2 since I downloaded it.

----

Safari is out of beta! Download v1.0 today!

WebKit is public, too! Log into connect.apple.com to get it.  Very nice API.

----

Yeah, and right after I finished reverse-engineering it. Argh. --AdamAtlas

----

I'm running Panther and the install for the WebKitSDK is giving me a "You cannot install this software on this volume. This system cannot use this software."  Any ideas?

-- EricFreeman

**Maybe the Xcode/Panther install already includes it?**

No, I don't have Xcode installed.

If you did, you would see that WebKit is a part of it.

