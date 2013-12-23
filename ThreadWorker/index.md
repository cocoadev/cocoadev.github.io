---
layout: page
title: ThreadWorker
---



          General/ThreadWorker is a class whose sole class-level method
          makes it easy to offload a task to another thread and
          notify your application when you've completed the task.
          Creating General/MultiThreaded applications with Objective-C
          in Mac OS X's Cocoa framework is easier than raw C code,
          but there are still some difficulties that General/ThreadWorker
          will help you overcome.

Calling General/ThreadWorker looks something like this:
    
    General/[ThreadWorker workOn:self 
                  withSelector:@selector(longTask:) 
                  withArgument:someData
                  didEndTarget:self
                  didEndSelector:@selector(longTaskFinished:)];

In this case, longTask would be run in another thread, and longTaskFinished would be called **on the original calling thread** when longTask is finished.

You'll find the latest information and source at http://iharder.sourceforge.net/macosx/threadworker

Enjoy!

-General/RobertHarder

----

Is it safe to access UI elements using General/ThreadWorker?

----

It is safe as long as you only edit or access UI elements from the longTaskFinished method only.

For example, something I do a lot is:

longTask - connects to a server and gets some data

longTaskFinished - put that data into some UI list model

--Gabe

----

But the example code shows how you can pass in a UI item and access it as long as it's passed through the userInfo dictionary. Is that the valid way to do it with General/ThreadWorker?

----

The example is actually very dangerous and wrong. General/AppKit is generally not thread safe, you should *never* message a view from a thread other than the main thread. So in particular, you cannot method a UI element from the longTask method.

----

The example application does *interesting* things when you close the window while the task is running. I applied some trivial changes to the project to attempt to work around this behavior, but it only works most of the time. It is still easy to crash the app if you start the task and close the window too quickly. I would love to see an elegant way to handle the notion of the UI objects getting deallocated while the long task is still running.

----

Well, you could create an General/NSObject in mainmenu to control thread i/o and act as a proxy to the UI.

----

I'm not sure where the best place to put this is, but an alternative implementation of the same idea (with reused threads and lower communication overhead) can be found at http://www.maven.de/2007/01/cocoa-worker-thread/

----
I've written something very similar in General/ThreadPool. Didn't really think of how it would work with a UI as I wasn't thinking about how to get it to do stuff whilst leaving the GUI responsive but how to get stuff on more done of several cores at once.

Stefan
