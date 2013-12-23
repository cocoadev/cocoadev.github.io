---
layout: page
title: ApplePascal
---



The native high-level language of the original Macintosh. Lingers on in the "pascal" directive, Pascal-style length-prefixed strings, and various other artifacts.

Apple's history with Pascal started with the UCSD p-system, continued with the Lisa Workshop, Macintosh Pascal, and MPW. Pascal was the basis for Apple's first three application frameworks: the Lisa Toolkit (written in Clascal) and MacApp 1 & 2 ( written in Object Pascal); and for Think/Symantic's Think Class Library (TCL).

Pascal pretty much left the Mac scene with the advent of the PowerPC Macintoshes. But Object Pascal lives on (at least in the Windows world) as the language used in Borland Delphi.*

*Delphi rocks, but I'm comin' 'round to the Cocoa way of doing things.

----

Delphi doesn't rock enough if it's not on Mac OS X :) -- RobRix (who would gladly play with it if it were)

GNU Pascal, a GCC front-end, aims to support the Delphi dialect. There is also the FreePascal compiler, and it's associated Delphi-like IDE called Lazarus.  A MacOS X build of GNU Pascal is available at http://www.microbizz.nl/gpc.html, even with Xcode integration and syntax highlighting. The only drawback is that it does not have Cocoa support. You can write apps using the Carbon APIs however.  On the other hand, Lazarus is a full featured and standalone GUI IDE for Mac OS X, Windows, and Linux, but the mac OS X version of Lazarus requires Apple's X-Windows server to be running to use Lazarus on Mac OS X, and is as far as you can get from being a native or Mac-like IDE. 

----

Pascal developers may also want to take a look at the Objective Modula-2 project (ObjectiveModulaTwo) which is defining a set of extensions for Modula-2 to support Cocoa by adopting the Objective-C object model and hook directly into the Objective-C runtime. It even uses the same bracketed Smalltalk message-passing syntax. The extensions will be implemented in the GNU Modula-2 compiler, another GCC front-end.

----

WarrenP says:  Hi guys, I'm a Delphi developer learning Cocoa.  I am of course trying to figure out if I should (a) dump my ObjectPascal inclinations and embrace objective C, or (b) if I should #import<delphi> syntax into the Cocoa world, via writing my own precompiler/preprocessor that accepts an object-pascal like syntax.  It looks like ObjectiveModulaTwo is interesting as a spec, but I don't see that anyone's done any implementation on it, and besides it maintains ObjC's method invocation syntax, which is part of what I'd nuke if I went to the pre-processor route. Anybody else learning Mac/Cocoa development could email me, if there's enough of us, maybe we'll make a mailing list.  *Updated Six months Later:* WarrenP has realized that doing OS X development in anything other than pure ObjectiveC+Cocoa is not going to fly.  This leaves him unhappy due to lack of portability of the results. Ideally he'd like to have Delphi for Mac.  Second best would be a way to write something for the Mac that he doesn't have to rewrite totally if he's moving back to Linux and Windows. RealBasic is not good enough.  Sigh.

----

Hi WarrenP. Just a suggestion, but you're probably going to get a lot more work done if you embrace ObjC in lieu of writing a preprocessor. I've been down that road, and it's never been as comfortable as using the language the way it's meant. -- RobRix

----

Objective-C and Cocoa are very intertwined.  WarrenP, you'd do best to learn Cocoa first using its native language, Objective-C, so you can learn how it's intended to be used and how its design patterns all fit together and build on top of language features.  Then, if you still want to, you'll be in a much better position to create a bridged language or translator for a different language.

----

"...has realized that doing OS X development in anything other than pure ObjectiveC+Cocoa is not going to fly." Precisely the reason why the Objective Modula-2 project decided to keep the Smalltalk style message syntax and hook directly into the Objective-C runtime. This probably makes a lot of sense. BTW, according to this: http://objective.modula2.net work is under way on a reference implementation.

----

Development versions of the Free Pascal Compiler now support an Objective-Pascal (ObjectivePascal ) dialect that also allows direct interfacing with the Objective-C runtime and frameworks. The regular Object Pascal syntax has been kept here though.

