---
layout: page
title: OtherLanguages
---

**Please add information about languages which support Cocoa natively in CocoaLanguages, and those which do so via a bridge in CocoaBridges**

Native support means the language uses the Objective-C object model and hooks into the Objective-C runtime directly - no translation.

Bridge support means the language's own object model is bridged to the Objective-C object model through translation.

----
Has the issue of Cocoa development with languages other than Obj-C or Java been discussed somewhere here? 

Several choices exist:


* .NET: NObjective [http://code.google.com/p/objcmapper/]
* Perl:  CamelBones [http://camelbones.sourceforge.net/concepts/readobjc.php]
* Python: PyObjC  [http://pyobjc.sourceforge.net/]
* Ruby: RubyCocoa [http://rubycocoa.sourceforge.net/doc/]
* Lisp:  OpenMCL [http://openmcl.clozure.com/Doc/cocoa.html]


http://www.fscript.org/links.htm provides a list of languages bridged to Cocoa.

----

This is the first time I came in, but could not find anything with the search or otherwise. So took the liberty to add a note in the CocoaMostWanted (to see if anyone else would want it :) and mentioned PythonCocoa that I'd like to use myself and RubyCocoa that just found on the net (in Japan, see http://www.imasy.or.jp/~hisa/mac/rubycocoa/ ). 

Perl people actually tell that Apple has an internal perl<->obj-c bridge for this! < http://archive.develooper.com/macosx@perl.org/msg00768.html > -- ToniAlatalo

----

Unfortunately, I think that you are in the minority.  It's already a miracle that Apple got the Java bridge together. I think other languages would be very unlikely.

*The ChangeLog for RubyCocoa lists many contributions from someone @apple.com.*

----

It doesn't necessarily have to be an Apple-made technology, I wouldn't think, but it's still probably not very likely that anything will be released for binding other languages to Cocoa. Which is unfortunate...

-- RobRix

----

Well, there's the AppleScript - Cocoa connection. That could be a starting point. At least it's technically possible to replace AppleScript with another language via OSA.

(see also: http://developer.apple.com/techpubs/macosx/Carbon/interapplicationcomm/OpenScriptingArch/openscriptarch.html ) 

Of course this probably would amount to basically re-writing the language compiler / interpreter in question, so I doubt that it'll happen. But if it did, you should be able to access Cocoa from there in much the same way AppleScript does it since AppleScriptStudio.

-- JensBaumeister

----

I've been kicking around the idea of a limited DyLan-ObjectiveCee bridge. I don't think the technical obstacles are insurmountable, except one: the only Mac OS X Dylan implementation, GwydionDylan, can't do multi-threading
-- Dustin Voss

Let me add that the Dylan-C bridge doesn't appear to support vararg C function calls. That is another difficulty.

----

The Mono people (and at least one Apple employee) are working on a Cocoa bridge, unimaginitively called Cocoa# (CocoaSharp) [http://forge.novell.com/modules/xfmod/project/?cocoa-sharp]. No formal release yet, but the current bits are available in Mono's anoncvs repository, the module name is Cocoa#.

