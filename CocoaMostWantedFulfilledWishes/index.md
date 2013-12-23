---
layout: page
title: CocoaMostWantedFulfilledWishes
---

----**Fulfilled Wishes:**----



* *ProjectBuilder should be at least a little faster*

Tired of sipping coffee waiting for it to compile ;) *I voted AYE, but since that's often the only time all day I get to sip coffee perhaps I should change my vote ;)*

Technically the problem is in the build system, not PB...PB is just the GUI around it

Note that if you make good use of precomps, you can get some pretty acceptable turnaround speeds...  Still be nice if it could be even faster though.

**Outcome:** ZeroLink, predictive compilation.

* * Code completion, on/off option of course. Popupmenu with possible completions. Interactive syntax coloring with errorchecking.*

Basically, some nice features from the Java IDEA IDE. Like, reference a class not imported? It gets automatically underlined, like typing errors, and a bar right next to the scrollbar shows red dots for lines with errors.

Yeah, definitely. IDEA is the best IDE I have ever seen, or even heard of.

It sounds like Xcode will address this.

**Outcome:** Not as good as IDEA, but all the most-wanted features are present.

* *Fix Xcode's syntax coloring*
Currenly (v 1.1) Xcode only colors language keywords and comments. It should color variable names and everything else from prefs as well.

**Outcome:** It does variable names, and any word you've recently used. I'm not sure what you mean by "from prefs".

* *Add a 'hide application' item to the contextual menus for applications in the dock.*

Panther has this =)

*Also fix proxy icon dragging, it's exactly backwards from Carbon/Classic that way.

Proxy icon dragging is consistent between Carbon and Cocoa in Panther.

*Provide a clear, simple way for developers to file feature requests against the API--bugreporter doesn't look appropriate, and I can't find any other forum. (Votes: AYES: 3 NAYS:0)

See the Project Builder or Xcode release notes. There's a mailing list you can mail suggestions to.

*Add the possibility to show and hide views, meaning any subclass of NSView... Something like : *(BOOL)isVisible* and *(void)setVisible:(BOOL)flag*

Panther supports showing and hiding of NSView.

�Unfortunately, hidden views still participate in resizing. They are just a big blank spot.

* *C++ like exception handling in Objective-C* (Votes: AYES: 2 NAYS: 0)
* *Java-style exception handling in Objective-C* (Votes: AYES: Oh, Just Implement It NAYS: 0)

In Panther, Objective-C has C++-style unchecked exceptions along with Java's finally block. Sold.

Exactly what is the difference between Java and C++ exception handling? Both have try/catch, both throw objects, but Java needs you to declare exceptions and allows finally. Is that it?

The good: Java exception handling also has the "finally" block which gets executed after the try and catch clauses regardless of whether an exception was thrown or not and regardless of if the exception was caught or not. 
The Bad: Java exceptions are CheckedExceptions, which means that you have to deal with them, and can't just let them bubble up (without writing throws). CheckedExceptions are quite controversial. Most modern languages have decided to go with unchecked exceptions.     

Actually, Java has both checked and unchecked.  Any subclass of RuntimeException can be thrown without being declared.  Descendants of Exception must be declared.

* *ProjectBuilder should integrate easily with external text editors* (Votes: AYES: 6 NAYS: 0)

The built editor is just plain missing a lot of features I use on a daily basis in BBEdit.

I agree with the above comment. Not only does it lack features, if I move a file it loses track of it like the mindless TextEdit.app

**Outcome:** Implemented in December 2002 DevTools. Search the Project Builder release note (in the Help menu-- "Show Release Notes") for "External Editors."

* Add search capability to Apple's Cocoa-Dev mailing list. Is the lack of search capability a good indication of the POWER OF WEBOBJECTS? (Votes: AYES: 3 NAYS:  0)

**Outcome:** As of today (Aug 16th, 2001), Apple's lists are searchable - not by the means of WebObjects, though but rather by using htdig...

See http://lists.apple.com/search

**2nd Outcome:** As of today (Aug 28th, 2001), COCOADEV and MACOSXDEV mailing lists are now both archived and searchable on my website.

See http://www.cocoabuilder.com (it also has Xcode-users archive).

* *Stop defaulting to Internet Explorer every time I turn my back.* (Votes: AYES: 15 NAYS: 0) [note7]

**Outcome:** Seems to be solved in Jaguar.

* Let me disable the darn dock (or at least make project builder aware of where it is!)

**Outcome:** I solved this by running
    sudo mv /System/Library/CoreServices/Dock.app  \ 
        /System/Library/CoreServices/NoDock.app
, then killing the dock process (or rebooting), then downloading ASM 1.3.1 and running it (being sure to launch it at startup in the preferences). See http://asm.vercruesse.de.

* Come up with a coherent recommendation on creator/file types. See  http://arstechnica.com/reviews/01q3/metadata/metadata-1.html (Votes: AYES: 1 NAYS: 0)

**Outcome:** Apple posted the following message to describe their recommendations. Not the ideal solution but hopefully its just short term until they implement a hierarchical typing system. See http://cocoa.mamasam.com/COCOADEV/2001/09/1/11740.php

* Stop launching Software Updater for stuff I've already said I don't want!

**Outcome:** Actually, this is already done, as I found when I searched the menus for Software Update. Hurrah for Apple! -- KritTer

* *Publish a much better book than "Learning Cocoa".* (Votes: AYES: 17 NAYS: 0)  [note1]

**Outcome:** See CocoaBooks. Also, Vervante can now publish Inside Cocoa.

Also...  Apple in team with O'Reilly has in fact just released a new book titled, "Building Cocoa Applications".  It is far better than the original "Learning Cocoa" book.  (Which, by the way is being completely revised and rewritten to include chapter(s) covering the Objective-C language too!)

I want some better O'Reilly books.  They've always been so good, but with Objective-C/Cocoa they haven't really been the best.  The follow one flagship app and miss a lot of key points.  They seem to fluctuate between very beginner things and very complex things.  We need a lot more intermediate material.  I found that the hardest when I started learning Objective-C/Cocoa.  I want more stuff like Cocoa Programming by Anguish/Buck/Yacthman.  That's more like the O'Reilly I remember.   Straight and to the point.  Almost reference material but with clear explanations of concept.  And jam-packed.  A variety of examples, not simply the things that have to do with the app the book is talking about.  Hillegass stuff is great too.

* *Classes for parsing XML files/data into NSDictionary* (Votes: AYES: 6 NAYS: 0) [note4]

I voted AYE, but I would much rather see a full blown XML parsing framework, something along the lines of Xerces, with full support of namespaces, XSchema, etc ... with a convenience category for parsing XML into NSArray/NSDictionary. --PeterLindberg (Votes:AYES: 4 NAYS:0)

What's wrong with the XPat ObjC parser (find it on stepwise)?

Or Iconara DOM framework ( http://sourceforge.net/projects/iconaradom ) , which I've been using and it's excelent - it uses the Carbon XML classes underneath. Nonetheless, I'd like to see apple integrate some kind of XML parser in Cocoa out of the box.

**Outcome:** NSXMLParser was introduced in Panther




----**Documentation**----

* *Fill in the freakin' documentation!* (Votes: AYES: 37 NAYS: 1)

Apple says they updated their docs in December but there is still A LOT missing - 12/21/2001

The April dev tools CD that will be released soon (it was released as Beta on ADC a couple of days ago) is said to contain complete Cocoa docs. --PeterLindberg

And make it searchable with something other than cruddy Help Viewer!  [Doesn't everybody use CocoaBrowser? Its glorious. -- JayPrince]
[AppKiDo http://homepage.mac.com/aglee/downloads/ is also very nice -- Bruce]

Note: Apple claims to of completed all of the documentation up to Mac OS X 10.1.  It can be viewed at: http://developer.apple.com/techpubs/macosx/Cocoa/CocoaTopics.html

But they're just talking about the reference docs, not the conceptual stuff. And it's not true, anyway. NSRulebook? NSTypesetter? Hello?

What I really want to see in Apple's docs is the retain state of what is returned by class members, convenience members etc.  Especially when the creator is a class method- the docs say "Creates and returns a new X with Y." Is it retained or autoreleased?  There's a tradition that governs this behaviour, but it should be documented on each message 

It's a simple rule: it's always autoreleased if you're not using the +alloc method to create it or the -copy method to duplicate it. That's the universal convention, and there are no exceptions (excepting perhaps bugs in the implementation).

* *Use links properly in existing documentation* (for instance, link to types and constants). (Votes: AYES: 18 NAYS: 0)

* *Publish XML version of documentation*, to allow for many formats (Votes: AYES: 6 NAYS: 3)  [note2]

Why the NAY?  XML Sucks and its complicated.  Just do HTML.

Huh? You can generate HTML from XML, and Apple will do so by default. How would XML hurt you?

* *Accept user contributions to the documentation* (like php.net) (Votes: AYES: 1 NAYS: 5)  [note2][note3]

I think sites like this fill that gap. -A nice link to cocoadev would be cool but would it be admitting that their documentation isn't great?

* *Apple should hire Bruce Eckel*, teach him Cocoa; then, have him write the Cocoa Docs (Votes: AYES: 5 NAYS: 0)

Who?

http://www.mindview.net/ - Bruce writes Object Oriented programming books, all of which are available online.  A Cocoa book written by Bruce might help bring more people into the community.

The documentation is pretty amazing rocking good nowadays, especially that great redesign. Can we move this to CocoaMostWantedFulfilledWishes now? �Brent (2004.02.27)

Sounds good to me. �DustinVoss



----
----

*Back to CocoaMostWanted*

