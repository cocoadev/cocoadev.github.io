---
layout: page
title: PreventingFileAccess
---



I will be writing some code that will be writing some large files.  While these files are being written, I'd like to prevent other threads and/or applications from accessing the files.  How can I lock the files to achieve this?  Changing file permissions isn't going to do the job, because one process/thread needs write access, while all others shouldn't be allowed access at all.

----
How are you writing the files? If you are using the Cocoa method     writeToFile:atomically:, just pass     YES for     atomically. This will write the data to a unique temp file first, then just move the temp file to its final destination. If you are not using Cocoa...well, I've seen other programs do this, but I'm not exactly sure how. --JediKnil

Cocoa isn't doing anything particularly magical. Just follow your exact description, write the data to a temp file, move it to its final destination. The only gotcha is that the temp file has to be on the same filesystem as the final destination, otherwise the move won't be atomic.
----
When it writes the file atomically (say using the     NSData class), where is it actually writing the file?  I don't see the file until it's actually complete (which is good).

I haven't researched this so I can only guess, but I would say that it writes it somewhere in /tmp, or to a dot file in the same directory as the target, neither of which would normally be seen if you're just looking casually.

You have actually stumbled across one of the cool things about OS X. When you tell an NS Object to write it's self automatically it doesn't write the file right away. But rather it writes it to a temp file and then renames the file when the write is finished. This is intended to stop corruption of the file system in case of a crash. In some special cases it is undesirable for this to happen, thankfully the gave us the option not do this. Original submitter, have you looked into using a combination of fopen, and lockf. I know they are pure C, but they may get what you want done. -- CurtisHoover

