---
layout: page
title: SequenceGrabbingACIImage
---

Is there any function in the Sequence Grabber API that would help me grab an image and create a CIImage?

----
I have no idea of QuickTime, honestly, but one short google search brought up toxicsoftware's blog with an example: 

http://toxicsoftware.com/blog/sequence_grabber_code_now_with_coregoodness/

source:

http://svn.toxicsoftware.com/public/trunk/Frameworks/ToxicMedia/


Did that help?

--marcocoa

----
I don't see a whole lot of use of the Quicktime Sequence grabber API in the example.  I don't even see references to the framework.

----

Make sure not to look into samples/SequenceGrabber but into /frameworks/source/sequencegrabber...

http://svn.toxicsoftware.com/public/trunk/Frameworks/ToxicMedia/Framework/Source/SequenceGrabber/

But as I said, I m not familiar with that thing, maybe it s of no help whatsoever...

----
It seems like a really useful user made framework, but is there anyway to actually download the whole thing?  I can just vew the source in the browser.  I can't compile it, or look at the nib files.  Is there any way or program that would download the actuall file?

----
It�s a subversion repository... use subversion to grab the files. You ll find info on subversion elsewhere, e.g. here:

http://developer.apple.com/tools/subversionxcode.html

--marcocoa
----
Problem solved.  This article helped:
NSImageFromAMovieFrame

All you do is use SGSetGWorld, and then use a method similar to the one in the article.

