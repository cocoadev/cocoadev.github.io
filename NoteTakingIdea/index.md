---
layout: page
title: NoteTakingIdea
---

My friend, an avid PC user but slowly budding OS X lover, expressed interest in finding a note taking application he could use on his iBook while in class.  He so far only likes MS Word for windows as it can open HTML files(which he sometimes gets notes for classes in) and add notes to it which show up in a cooler view than in MS Word for mac(along the right side with lines to where he inserted them... reminded me of FileMerge in a weird way)
He has yet to find a program on the mac that he likes, specially to take PDF or HTML notes and add his own notes to them while in class.... this brings me to my idea...

So can MacOS X easily composite various formats in some kind of NSView?  Think it would be relatively easy to create a Cocoa program that could composite notes on top of html and pdf files, making a really easy to use note taking program that you can use to add notes to already made documents.

Just a thought, haven't seen anything that could do something like this yet, has anybody else?

Any interesting thoughts on this idea as a little Cocoa project or something? :)

----

There are at least three applications that may help:

* NoteTaker  [http://www.aquaminds.com/index.jsp] might fit the bill.
* iOrganize [http://www.iOrganizeX.com/ or http://www.apple.com/macosx/gems/iorganize.html]
* OmniOutliner [http://www.omnigroup.com/applications/omnioutliner/]


----
Sounds cool... I reckon it would be good in conjunction with a tablet, so you could underline important bits and have arrows going this way and that and equations in the margin just like on real annotated notes. But that would take up too much space while taking notes in a class... maybe I'm just too used to taking notes on my eMate.

----
Yeah, just pondering how easy such a thing would be, be fun to be able to take notes on top of html/pdf/others.  

----

I hate to poop on  your party -- but unless you guys have the team and the funding it ain't gunna happen.  And here's why:  the thing about taking notes on top of most documents, is that they are generally resizable, whereas peices of paper are not :).  What this basically means, is that you circle something and mark that location with an X,Y coordinate pair.... and let's say you circled the sixth word of the third sentence.  Well, you resize your little doc window and poof, that coodrinate pair no longer matched the sixth word of the third sentence -- everything has been resized.  The alternative is to prevent your user from resizing... which means you have to introduce scroll bars where you really wouldn't want scroll bars... on small screens it would suck, and if you tried to view the notes you took on a small screen, on a larger screen,,, well, you wouldn't be able to take advantage of the bigger screen's extra realestate.  That little problem alone will shut you down righ there.  I can think of a few more.  If you're an iBook user, using your trackpad to enter in scribble style notes will suck.  Typing on top of something will also suck -- it will clutter.  I'd personally say the best peice of technology for that app is a good legal pad and pen.

**Resolution independence isn't that hard. I think this could be useful. Of course, I don't use my trackpad...**

The annotations would scale too, of course... and I did say tablet, not trackpad.

----
Also, i wasn't saying just scribble notes, more importantly is more of a sticky based thing that would draw a line to where the note is talking about, something like that i know would be kind of nice.  Word for windows does something like this, but not sure the scoop with it and small screens, since it easily could be too big.  but you sould be able to scale the notes properly just fine if you did scribbles at x,y, but i think putting notes pointing to spots would be more important
-JeremyK

----

Yeah, the issue isn't scaling -- it's window resizing.  Go ahead an resize your browser window now.  If it's a good browser, you shouldn't get horizontal scroll bars -- the text should just redistribute itself into the narrower space.  That's what I'm talking about.  Text doesn't have any fixed X,Y coordinates that are constant... and there is no realy way of predicting where text is going to go when you resize -- there could be pagination issues, font size, and style issues, there could be hyphenation... circling a word and having that cicle stay on top of that word as the window is resized is where the real bitch would be.

**PDF documents don't act like that. They zoom.** -- Sure, but HTML and TXT does act like that.

And if you're talking about tablets... leva cocoadev now -- there are no mac tablet computers -- unless you meant something like an input tablet pad... in which case I pitty the poor sucker that tries to use on of those on his lap with a laptop. :)
----
tablets are fun, can doodle and stuff. hehe

as for the word position, now this is where you can be all sneaky and shit, be creative, you can put marks on paper by line # and word # and even character matching... at least sounds like that could work in my mind, but who said my mind had grip on reality...
-JeremyK

----
I agree JeremyK has a good idea -- but that's only for text.  The beuatiful thing about this idea would be that you ccould do all sorts of document formats -- even .doc now with panther -- which is what teachers occasionally pass stuff out as.  So... these formats can mix images that can not only resize but also scale -- and then you have an even nastier problem.  My original comment that this idea wouldn't work out didn't mean to suggest it was impossible -- only very difficult and probably not something you could do a good job of in your spare time -- could be a solid year of heavy development to get something nice and usable and user friendly out.

----

I don't really see the problem.  Render text or HTML to PDF with some fixed viewing size, and then doodle away on the PDF.  Then let the application store the doodled-on document as a PDF wrapped with whatever data is necessary to recreate the doodles.  This shouldn't be that difficult, seeing as however everything that comes on screen in Mac OS X is rendered to PDF.  --OwenAnderson

----
Owen, the thing about that is if you can't even resize why the heck do you want to do all this on the copmuter?  A pen is easer to use than a trackpad, which is what most users have on their laptops (given that this idea is best suited for inclass situations).  Again, pen and paper seems like a more apealing technology than this app.

I never said I thought this was a great idea in the first place, but I don't see why you would want to resize.  Doesn't that just make it harder for the reader to remember visual cues in the document?  -OwenAnderson

