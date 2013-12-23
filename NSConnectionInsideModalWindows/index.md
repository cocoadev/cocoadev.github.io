---
layout: page
title: NSConnectionInsideModalWindows
---

I'm trying to create a plugin for aperture.  In my plugin, I spawn a thread, and use a pair of NSConnections to communicate between the threads.  The thread spawning goes fine.  But when the worker thread tries to retrieve the parent object through [serverConnection rootProxy], the code hangs.

This code works fine stand-alone (i.e., not inside aperture).  I figure it has something to do with the plugin window being modal.

Other things I considered:

1) inside the main thread, I call this:
        [workerConnection addRunLoop:[NSRunLoop currentRunLoop]];
   result: no difference

2) inside the main thread, I call this:
        [[NSRunLoop currentRunLoop] configureAsServer];
   result: no difference

3) maybe I need to send the NSConnenction a addRequestMode message.  I tried getting the mode of the main thread using this:
        DTCLog(@"current mode: %@", [[NSRunLoop currentRunLoop] currentMode]);

   output: current mode: (null)

any ideas?

P.S. I'm new to obj-c/cocoa, so please excuse me if this is a stupid question.  thanks!

----
Add NSModalPanelRunLoopMode.

Also, please don't post the same question to multiple locations. It just wastes people's time because they won't see that somebody else has already answered the question in another place, and pisses people off for wasting their time. http://www.cocoabuilder.com/archive/message/cocoa/2006/12/12/175786

