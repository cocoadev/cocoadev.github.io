---
layout: page
title: ArchivingANSMutableArray
---



I have a project with a class that conforms to NSCoding.  encodeWithCoder encodes three NSStrings and an NSMutableArray.
When I run the program it archives fine the first time.  If I run it again, dearchive the file I archived the first time, then try to archive again, I crash.

This doesn't happen when I //comment out the line that encodes the NSMutableArray.

PostYourCode. Without more information about exactly what you're doing, we can't tell you what you're doing wrong.

----
Forgive me.  I posted in haste.  I think I have a memory management issue.  Point taken regarding "posting code."
----
Ok.  I can't figure this thing out.
Now I know you guys want/need to see code in order to help me out, but I don't know where to begin!  I'm quite sure no one wants to go through my entire project (small as it may be) but I really have no idea where the problem is and what's causing it.
I suspect that it's a fundamental flaw in my understanding of Archiving or something.
help?

----

For all we (or you seem to) know, one of your accessors could be screwing up. You'll be far better off if you debug this yourself, because you will really learn something. Or else post the code. I think you should archive the array as an immutable NSArray instance, and then when you unarchive it into the mutable array ivar, you should make a mutable copy of that array. You implicate the unarchiving code in describing the crash. You may need to retain the array when you set the model ivar from the unarchived data. So there's several things you could look at. Magic is not at work here.

----

See MemoryManagement and DebuggingTechniques. If you're still absolutely stuck, decide which bits of code are likely to be relevant (hint: your unarchiving and archiving methods, plus related accessors), and post those.

If you're at a point where you are saying "I don't know what's wrong, and I can't even figure out which parts I should post, but nobody will want to see my whole project" then you simply haven't finished your work yet. There is so much you can do to isolate things further, including writing test cases, commenting out code, etc.

