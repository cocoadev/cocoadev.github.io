---
layout: page
title: MIMEEncodingPostsInCocoaHowTo
---



First I'll explain what I'm trying to do, to avoid any confusion. My company has developed a system which allows users to upload files through a web site, automatically storing the files in the right place, sending out notification emails, etc. That system exists; it's fine. This question isn't about that. 

What we're trying to do now is to develop a stand-alone client that allows the user to drag and drop files into a window, and then upload those files to the server. Rather than re-implement most of the features in this application, it was decided to have it simply interface with the website over HTTP, so the existing site will still handle placing them, sending emails, etc. This is all good and well, except that I can't find the Mac functions, Cocoa or otherwise, that allow me to supply a file and generate an appropriate mime-encoded version of that file. Searching Apple's documentation for "MIME" just turns up a lot of references to the QuickTime API which are too high-level to address the question. 

Something to acheive this certainly exists, though I'm not certain if it's interface is exposed. Such functionality is naturally used internally by the WebClient and related classes in the WebKit but I've been unable to find any way to access them independently of those constructs; simply having a hidden web view and interacting with it programmatically seems inefficient, to put it mildly. 
What I'm looking for are functions that take a filename and returns an object containing the appropriate mime information - mime encoding type and the encoded file itself. Failing that I'll settle for any workable solution. Cocoa routines would be ideal, followed by C or C++ code for Mac OS X 10.x. Failing either of those, anything reasonably portable will do.

Thanks in advance for any help with this problem.

Edit- addition -
Just wanted to add that while I've been programming for years, I've done little network programming (and that was very low-level) so I may be approaching this problem in the wrong way. I think I explained what I'm trying to do well enough that more network-savvy programmers will be able to point me in the right direction if I'm currently looking in the wrong one - a possibility that seems likely, given my failure to find info on what must be a fairly common problem.

--Will

----

Sounds like you're wanting to do an HTTP POST with a file upload, right?  Check out these topics:  HTTPFileUpload and POSTMethodANDNSURLRequest.

----
Thanks, that looks like it'll do the trick. Sorry for reposting something that had been covered; I can't claim to have done an exhaustive search first, but it had been a long day. I kindof figured this would have a simple answer, I was just frustrated at not finding it yet so i decided to just ask the experts. Never fails! 

And just a random comment, I've been programming in C and C++ for almost 2/3 of my life (since I was 10) and the past week is the first time I've programmed for an apple system since my first year of Applesoft Basic on the ][GS. They've come a long way, Cocoa/XCode are an excellent combination. 

--edit--
One more thing, I can use CURL if I must, that's no big issue, but I'd rather do it all natively in Cocoa if I can. From the thread HTTPFileUpload it wasn't clear that the approach used would work for BINARY files, his test file was text. Also, do Cocoa's native routines support SSL? If not I'll just have to go with CURL, which is at worst a minor inconvenience...

--Will

----

If you want to go native, see CFNetwork.  http://developer.apple.com/documentation/Networking/Conceptual/CFNetwork/Introduction/chapter_1_section_1.html

----

aha! Very good, many thanks! That definately looks more like what I need; I wasn't finding any info on enabling SSL connections in the cocoa networking docs, which was worrying me a bit as SSL is essential to the project. I'll have to work out how to interface with it a bit, wrap it up into a cocoa-friendly management class. I don't have a run loop (at least, not an exposed one; dunno if Cocoa apps have a hidden run loop or not?) but I should be able to acheive the same thing with a timer... Thanks again!

--Will

----

Ok, everything went pretty smoothly. Got a pretty clean interface for building up post requests by adding in two kinds of elements, variable and file. Only one problem. I can't find anything, online or in the docs, that would let me rig a deterministic progress indicator to the process. The program is intended to be used to upload multimedia files, sometimes extremely large ones, so a progress indicator is essential. It seems like there should be some way to track the progress, either just finding out how many bytes have been transfered so far on a socket, or possibly how many chunks it's been split into and how many of those have been sent. I've searched this site first and failed to find anything on the subject, and I've turned up dry in searching the documentation as well. Any help will be appreciated.

--Will

----

----
Will,
I can explain how to do this in C++. Hopefully, someone more skilled in Objective-C than me can explain how to do it in Cocoa.

In C++, I would create a custom streambuf that contains a callback to update the progress meter every time the underflow() function gets called. After calling the progress update, it will then call underflow() on the existing streambuf, which, in this case, would be some kind of network socket streambuf.

Is this kind of thing possible in Cocoa?
----

----
As far as I know, it can't; the classes I would have to extend are all hidden in internal classes which I don't even have headers for.
I may be wrong, hopefully someone will clear this up.

Will
----

