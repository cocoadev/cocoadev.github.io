---
layout: page
title: HowToDeleteWriteProtectedFiles
---

Describe HowToDeleteWriteProtectedFiles here.

Newbie here.  I have a simply little app which (ideally) deletes files specified files in /System/Library.  This directory, of course, belongs to System, and the users cannot delete these files by default.  isDeletableFileAtPath verifies this.  

My question is, how do I go about deleting these files?  My first hunch would be to use the SFAuthorization APIs.  However, to my untrained monkey eyes, it appears that the SFAuthorization APIs are geared towards Unix commands, not the NSFileManager methods I am using.  For example, it appears both a path and argument must be supplied when creating the Authorization Rights.  Is this the case?  Or do these SFAuthorization APIs work with NSFileManager?  

Thanks in advance.

Max 

----

According to a Cocoa FAQ [http://www.alastairs-place.net/cocoa/faq.txt] you shouldn't be performing privileged operations through Objective-C and Cocoa because the framework can be easily exploited. However, you could write it in C using Authorization Services (a C API). Here's some docs and sample code:

http://developer.apple.com/documentation/Security/Conceptual/authorization_concepts/index.html

http://developer.apple.com/documentation/Security/Reference/authorization_ref/index.html

http://developer.apple.com/samplecode/AuthForAll/AuthForAll.html

Good luck! -- RyanBates

----

Permissions are applied on the process level. That is, it's impossible to call a method or function with elevated privileges. The way to accomplish something with root permission is to spawn a subprocess that has the required permission, and let it do the operation. In this case, you would have a small helper program that would delete the files for you. The idea of "working with NSFileManager" is nonsensical.

----

Obligatory question (since no one's asked yet) - why do you need to delete stuff under /System ? It doesn't sound like a very good idea to me.

*It would be a pretty shitty virus if it didn't!* -- NotTheOriginalPoster


----
This was simply a simple little tool designed to remove some preferences and frameworks for QA purposes.  This is an app under-development, with many files in various locations which need to be removed prior to running various test cases.  

I ended up rewriting the code to use the Authorization Services API and am now good to go.  Thanks.

----

You are aware that you shouldn't need to remove app-installed items from /System because unless you happen to work for Apple, you shouldn't be putting them there in the first place, yes?

----

Well, theoretically you could if you bypassed the standard filesystem with say, ASM.
What I'm saying is if you used Assembly to control the drive directly, you could get the write head to over-write information...
But then, you'd have to understand everything about how a filesystem works, and know everything about HFS+.
It's possible, but not probable. I doubt anyone with the skill to bother with that would, they would find an alternative.

----
Actually, I do work at Apple; hence the files being placed in /System/.  ;-)   Thanks for the help, everyone.  
-Max

----
Well gee, I should hope that you would have buddy's at work that are well qualified to answer your question. :)

