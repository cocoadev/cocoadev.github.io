---
layout: page
title: ObjectLibraryRefactoring
---



The ObjectLibrary page had grown to unmanageable size, and it had become impossible to add new items because the page was too big. I handled this by splitting off the two biggest sections -- Controls & View Classes and Networking -- into separate pages: ObjectLibraryViews and ObjectLibraryNetwork. The root page contains several references to each: a     \\[Topic\\]�section near the top, the change log, and their respective original positions in the list. Note the 

It is my opinion that this should be done for most or all of the remaining sections, but of course people may disagree. If doing this for another section, note the similar formatting of the subpages, and the use of a Topic to create a table of contents on the main ObjectLibrary page (see HowToUseTopics). - JensAyton

----

Splitting the ObjectLibrary page into sub-pages is probably ok, but why do we need a separate page explaining it when the WhatsNew page exists? PLEASE DO NOT CREATE NEW PAGES TO EXPLAIN WHY YOU'RE CHANGING OTHER PAGES ...

----

To the above:  creating discussion pages rather than sticking discussion in the middle of info has been recommended in the past..  Think of wikipedia, where every page has an associated 'talk' page.

I prefer having all the classes in ObjectLibrary on the same page, though I'll live with it if you split them up.  Why does having them on separate pages make anything more manageable?  It seems the same to me, except that I have to check through multiple pages if I don't know what page might have a bit of functionality.

----

The separate page for explanation was originally made from CocoaDiscussions, which in fact encourages the creation of new discussion pages. However, it was really off topic there. I apologise for not having a perfect site map in my head. ;-)

The primary reason for the split-up was that the server refused to let me add anything to the page, because it was too big. (It claimed the limit was 32 kB, which is a lie; the page was near 64 kB.)

Wikipedia-like talk pages would make this place a lot less messy. - JensAyton

----

Ah, okay.  Then I prefer to leave the other sections inline until the page grows enough to require moving stuff out.

----

A better strategy for managing the size of this library might be by operative OS version; many of these classes are obsolete as of 10.3, many as of 10.4, and many as of 10.5.  So you'd have sections "Objects useful on 10.3 and earlier", "Objects useful on10.4 and earlier."

