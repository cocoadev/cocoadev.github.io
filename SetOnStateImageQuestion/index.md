---
layout: page
title: SetOnStateImageQuestion
---

In the NSMenuItem documentation, it says it's currently unsupported in OS X. Now I wonder, is there a way to make it work? A carbon-call maybe so I can use my custom-drawn NSImage as onStateImage, additional to the "normal" image?

----

1: Please do not post MailingListMode (question/answer) pages. This isn't a mailing list.

2: You might try NSMenuItem's -setImage: method ... the on-state image is the way it is because menus are a basic part of the UI and consistency is painstakingly enforced.


----
As stated above, I'd like to have it additional to the "-image" of NSMenuItems. and in my case, this would make perfect sense, so there wouldn't be much of a UI inconsistency.

Ad 1: How would I go about writing this question _not_ in mailing list mode? On the site that's linked to that word it just says to avoid it, but not what it actually is.

----
One way would be to write the question to an actual mailing list, such as the cocoa-dev list at Apple.

----

1: Did you actually *read* the MailingListMode page? It most certainly describes exactly what it is and the approach you should take on a wiki. The SingleQuestionAndAnswer (linked to from the afore-mentioned page) gives even more information.

2: As you read in the     -setOnStateImage: documentation, "Changing state images is currently unsupported in Mac OS X."  About 3 seconds into a Google search on that method found this: http://www.cocoabuilder.com/archive/message/cocoa/2004/8/30/116085 and this: http://www.cocoabuilder.com/archive/message/cocoa/2006/1/19/154789 and several others. 

This is why Q&A / MailingListMode questions are inappropriate. This page is not a handy collection point of notes but rather a temporal and transient discussion adding nothing to search results that can easily be found elsewhere (such as mailing lists via Google). Think of this site as a living reference book, not a threaded discussion forum. It's far more useful as the former and breaks down when used as the latter.

----
For everyone else who's interested in how to use -setOn/Off/MixedStateImage:, here's the solution (at least it worked for me):
Use an NSImage created with [NSImage imageNamed:@"..."];, it should work. worked here.
Whoever wants to reformat this page may do so, I can't because I have to read the MailingListMode thing first to see how to change it to make it pass the protocol.

----
The procedure is pretty simple. CocoaDev is foremost a living reference, so the procedure would be to take the information on this page and build a reference from it, whether a how-to or a list of resources or whatever format makes the most sense. First, it needs a better name, this one makes little sense for a reference. I would suggest something like NSMenuOnState, but other useful titles could occur. Write that page as a straightforward document using the information gained on this page. Then link it from other pages as appropriate, for example a link from NSMenu or UndocumentedGoodness would make sense. Last, mark this page with DeleteMe.

