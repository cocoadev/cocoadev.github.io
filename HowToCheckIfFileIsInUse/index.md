---
layout: page
title: HowToCheckIfFileIsInUse
---

Hi all,

Is there to check if a file is used by other application or not before deleting it from the disk. 

----

Delete it using Carbon. Carbon doesn't let you delete files that are in use.

----

Nor does Cocoa. If you look at the documentation for NSFileManager, you'll see its delete method (the exact name escapes me at the moment) returns a BOOL indicating success or failure.

----

Huh? Just because it can fail doesn't mean it will fail in the specific case of deleting a file that's in use. The underlying UNIX system allows deletion of files that are still in use, and there is nothing in the NSFileManager docs to indicate that removeFileAtPath:handler: will fail in this case.

----

My bad. I thought the documentation supported what I said, but going back and looking at it, I see that isn't the case. Cheers!

----

Why would you want to check if a file is in use before deleting it, anyway?

----

man lsof

----

There might be some circumstances where you'd want to check anyway, but it's impossible to check this reliably, because there's an inherent race condition.  Between the time you check whether a file is in use and the time that you take action based on that decision, the file may become in use or vice versa.  It's generally better to use a method that tries to do whatever you're interested in and returns an error code if the operation fails.  This is how functions like open() work.

----
There's no inherent race condition if you use Carbon. Or if there is, it's Apple's bug, not yours.
----

I'm saying there's a race condition if you first check if something's in use, then do something based on that check.  There's no avoiding that.  I'm suggesting that the poster use a BSD, or Carbon, or Cocoa, or whatever API that instead tries to _do_ something and tells you if it fails.

