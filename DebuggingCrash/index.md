---
layout: page
title: DebuggingCrash
---

My program just went into the weird zone.  It has an embedded framework.  It works great, except for a bug I found - so I tried to debug it.  But whenever I try, it crashes even before it gets to the main routine.  I built and ran the application again, and it worked great except for the bug.  It only crashes when I debug it, on some routine that's called     __dyld_link_edit_error It started doing this after I added only 2 lines of code, then it blew up.  Any help would be greatly appreciated. -RossDude

----

This might help: http://haoli.dnsalias.com/forums/viewtopic.php?t=57249

And this: http://www.cocoabuilder.com/archive/message/cocoa/2004/6/24/110469

----

Sorry, none of those did it.  I am sure that the optimizations are off, it's generating debugging symbols and so forth.  The weirdest thing of all is that it runs perfectly through the UNIX gdb.  But since I am not familiar with the gdb under UNIX, I can't solve my problem.  I did not change any build settings, and even cleaned and rebuilt my project.  Still nothing.  It starts getting these errors when it is using something called __dyld_load_executable_image and gets a SIGTRAP signal.  This is a very large project, and I don't want to JustStartFromScratch. -RossDude

*Have you added any resources lately? (images, nibs, etc.). Perhaps one is corrupted. You may also want to try creating a new target and seeing if that works correctly.*

Nope.  Just changed 2 lines of code and it screwed it up.  It screwed up two targets already.

----

So: the two lines you added are all that is causing this behavior, and then, only when you use the debugger.

Well, can you revert those two lines and get it working again (that is, within the debugger)?
Or did the bug itself appear when you added those lines? (Stupid question, I guess.)
Of course, if those two lines are intended for debugging, this may be a useless suggestion.
What ARE those two lines? Do they have anything to do with the embedded framework, or was that
framework used before you added those two lines?

I'm not sure that you can assert that it runs perfectly through the UNIX gdb if you are unable to work your way all the way through the code,
using breakpoints, and inspecting your storage.
Perhaps all you can assert is that it STARTS perfectly in the UNIX gdb.

----

Have you tried first cleaning the target, then second rebuilding the index (shot in the dark, but it's fixed some VERY odd seemingly unrelated issues)?

Can you create another project and add those two lines to it to see if the problem follows the code?

Can you .... I dunno, SHOW US THE LINES? ;-)

