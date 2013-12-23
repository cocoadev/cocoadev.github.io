---
layout: page
title: DocumentationMostWanted
---

Apple's Technical Publications Feedback page is at http://developer.apple.com/documentation/techpubsfeedback.html.

----

Complete formal grammar and skeletal yacc parser + lexer  for the Objective-c language. 

Can't find one anywhere, although the c formal grammar should be fairly easy to extend with the grammar described in "The Objective C Programming Language" and then this could be turned into a parser. Ideally it'd be using flex | bison as they are available on almost all UNIX flavors, but other systems such as ANTLR could be used. GCC uses a yacc parser, but it is too complex and coupled to the rest of the GCC ObjC compiler, at best could only be used for hints. Even only a header file parser would be very useful. By skeletal I mean a parser that parses the source and doesn't do anything with it. If anyone is interested in this I have a partial version which I am working on.

----
I personally think some documentation for the Omni Frameworks would be nice.  Some of the methods are so dang cryptic!

----
I think we need more documentation for bindings! **YES!!!  As well as a list of bindings the Cocoa classes have. -RossDude**

*A list of classes and their bindings is available at -
http://developer.apple.com/documentation/Cocoa/Reference/CocoaBindingsRef/index.html
-- PhilipQuinn*

----
And a nice converter.app tutorial on making an application apple scriptable... :(  I had to depend on the kindness of strangers and looking on a variety of examples... I spent days and days getting all the apple script functionality into my app... it should have taken me a few hours at most.

----
A detailed tutorial with source code examples on how to include receipt validation in your application. Apple's documentation is extremely thin and asn1c is more or less incomprehensible.

Echo that ... Figuring out how to make something applescriptable is a MAJOR PITA right now, at least as far as I can tell. Might go along with the fact that applescripting itself is a PITA, but it's very handy for some things. I'd love to add AS support to some things, but don't know where to begin.

----
If, as they say, a picture's worth a thousand words then a good example has to worth at least 750. Especially when you're new to some ObjC class, it would sure be nice to see a small example for the class overall and (or) the methods. Take something like NSColor. If you're new to ObjC, good luck figuring out quickly that colors aren't mutable or that you (may) need to retain/release them. A small example with comments would sure help orientate the reader and point out some of the more unobvious but important issues.

In more general terms, language documentation tends to focus too exclusively on specific mechanics (e.g., class and instance method syntax) but this is "second level" information. First level information deals with "how do I ...?" type questions. You go to the documentation first because you're looking for an answer to "how do I do *x*?" It's only after you have the general idea of how to do *x* that you start wanting to know the specifics of some method. The ObjC docs (and cocoa, carbon, xcode, ...) don't do a very good job of providing "first level" entries into the "second level" information. They aren't alone; most language docs are the same way but I bet Apple could do a better job of it.

----

Be sure to file bugs about these things at http://bugreport.apple.com -- if you've seen they recently added docs to NSArray and co. that more clearly explain that they're immutable and that there's a mutable variant as well, and links that lead back and forth between the two. I filed a bug about this about a month or two prior and they added it. maybe they were about to do that anyway, but I like to think it was due to my bug that they added this info. But even if it wasn't, the more bugs are filed against an issue, the higher the priority is to the Apple folks to get it fixed. -- UliKusterer

----

I'd say it's probably due to the bug you filed. I've filed a couple bugs on the documentation and they've all been fixed within weeks. Apple seems to be really serious about improving the documentation lately (it's about time!)

----

Really? I filed my bug about YES and NO being in the wrong place back in July and it's still at "Open/Analyze." -- PhilipQuinn

----

*YES and NO being in the wrong place? wha? I hope your actual bug report was more detailed ;)*

----

And long, considering the triviality of the bug -- PhilipQuinn

