---
layout: page
title: PortingUnixAppsToCocoa
---

A number of Unix (BSD, Linux, ...)
applications have been ported to Mac OS X,
but most run on the command line or under X11.
I am interested in starting a discussion on ways
to port these apps to Cocoa
(using Aqua, appropriate packaging, etc.).

**
  Existing Ports
**

The FreeBSD community maintains a repository
of (5000+ and growing) ported applications,
known as The Ports Collection ( http://www.freebsd.org/ports ).
NetBSD and OpenBSD use variations on this technology,
but their collections are somewhat smaller.

OpenPackages ( http://www.openpackages.org )
is developing a common infrastructure for these collections,
to reduce duplication of effort, etc.
Apple is a sponsor of this work.

Dr. Michael Love has built nearly 2000 packages
from the FreeBSD Ports Collection,
using a modified version of the makefiles.
His efforts are available in The Darwin Collection CD set; a
package download site and CVS repository are in the works.

Here are some pointers to resources for existing ports of Unix apps:


  * Apple's Science & Technology Port list -
           http://www.apple.com/scitech/unixports/index.html
  * Gnu-Darwin -
           http://gnu-darwin.sourceforge.net
  * The Darwin Collection -
           http://www.ptf.com/tdc


**
Porting Issues
**

Are there any HOWTOs on porting X11 and command-line apps to run under Aqua?

**
Packaging Issues
**

Is the Ports Collection technology suitable for packaging Mac OS X apps?
----
Your best bet with porting cli tools as Aqua GUI apps is to get the tool working on the cli in Mac OS X and then wrapping it with a GUI layer which merely runs the tool with the options choosen in the GUI. This can run from simple/straight forward to complex/makes my brain hurt. The place to start in Cocoa is with NSTask.

There is an introductory tutorial on wrapping cli tools with a Cocoa application at http://www.cocoadevcentral.com/tutorials, titled "Wrapping UNIX Commands."

With X11 stuff, porting difficulty will depend largely on how well divided the code is i.e., is the gui stuff well separated from the guts or is it intermixed freely.

----

It might be interesting to port some common interface toolkits like GTK+ and so on to use Aqua as a display mechanism, no?

-- FinlayDobbie

----

Interesting? Certainly. Possible? Possibly. My prognosis of how difficult it would likely end up being? Frighteningly.

-- RobRix

----

Mostly it would just (heh) require replacing all the X11 drawing code in GDK with cocoa code, I think... But how difficult this would be to do, I don't know. GDK is written in pure C though, so it wouldn't be as difficult as for C++ toolkits (like Qt)

-- FinlayDobbie

----

I was wondering, where's are the best online resources for learning Un*x programming? I'd love to port some stuff to Mac OS X, but I don't know what a lot of the Un*x and X11 headers and functions are supposed to do, etc....

I'd like to port (or help port) Sun's J2ME CLDC and MIDP reference implementation, personally...

-- WayneAndrewLee

----

One surprisingly helpful resource are the man pages on the system. Using man effectively takes a little practice. First try "man <function or keyword>". If that fails, "man -k <function or keyword>" will do a broader search for you. And I should use this opportunity to plug Xmanview as noted in my bio (MikeTrent) ;-).

For UNIX headers and X11 headers that aren't on the system, as well as OpenGL documentation when I don't have the blue book with me [now you can use "man <glcall>"], I tend to go to http://www.google.com/. Put the name of the API in the search engine and you'll find some poor sap whose gone to the trouble of documenting it on line ;-). 

For more introductory overviews I have no idea. I cut my teeth on The UNIX Programming Environment by Kernighan & Pike and Internetworking with TCP/IP by Comer. These days I use Advanced Programming in  the UNIX Environment by Stevens.

-- MikeTrent

----

Be warned that man pages are often wrong, out of date, or incomplete. This is a major problem and there are people trying to fix it, I'm just not sure how long it will take. From what I hear, this is a problem that's not only limited to Darwin but also to any other OS distribution out there.

-- FinlayDobbie

----

Good advice. I often double-check information in man-pages against information in the header files. Usually when man pages are wrong they're wrong in fairly obvious ways (wrong number of arguments to functions, symbols not defined right, etc.). The google search also helps identify conficts (one page doesn't look right? try another one!). And as any dinosaur will tell you the only real documentation in the UNIX world are the .c files ... 

But even with that disclaimer I find man pages very helpful in helping me understand what's supposed to happen, especially when I'm going to port an app from one API set to another.

-- MikeTrent

----

I certainly hope nobody ports GTK to OS X proper. The problems with two APIs are problematic enough without a third. At least Cocoa can be fixed, and by fixed I mean made to conform to the Mac HI (it has a high enough level of abstraction where it counts, sans file primitives). Look at the Qt 3 beta for an example of what I'd like to avoid: Win3.1 MDI apps with an Aqua-ish facade. ugh

-- PaulBayley

----

Confrontation aside, I think the idea was more to implement GTK in one of the existing API's (Cocoa being the one most of us here are more familiar with, of course). I personally wouldn't likely use any GTK apps because I have absolutely no reason to whatsoever, but it would be useful to some, I'm sure of it.

I agree that consistency in the UI is of primary importance here. And that that may take some work if someone's making GTK work with something it wasn't originally intended to work with. But if it's useful and can be done well, then it's worth it.

I don't think this is a matter which calls for argument. Merely discussion. In any case, if you want to make sure it's done right, why not do it yourself?

-- RobRix

----

	Why would you do such a complex thing when porting GTK+ to cocoa? All you need is a source-code level compatibility, so you'd work right on the level of the headers. You just begin to swap things (like gtk+'s window reference with a NSWindow or some intermediary structure that can handle what GTK+ asks of it) and so on and so forth. It would be a lot easier to do the GTK+ port at the high level, instead of at the low level. It wouldn't allow you to support themeing and whatnot, but who cares, honestly? 

-- DaveFayram

----

What are the legalities of producing shareware wrappers for tools that are often available with GNU public licenses? Does it depend on whether the tool is included in the shareware distribution? If I were to use such a tool to perform some function within my application can I still sell it?

-- DG

----

IANAL, but the generally accepted boundary where the GPL license "stops" is the addressing space of the GPL application -- an application is derived from GPL code if it loads GPL code in its own address space. It means that having the tool inside the bundle is OK (as long as you also make the source available for that tool) but not linking or copy-pasting GPL code into your own app. Xcode does it with GCC, for example -- EmanueleVulcano aka l0ne

