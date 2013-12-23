---
layout: page
title: ThreadSafety
---



Pages in this topic: [Topic]

When writing a General/MultiThreaded application, there are several issues that you need to be aware of: http://goo.gl/Cx9sQ



* **Memory Management:**
You have to be very careful while General/RetainingAndReleasing to maintain General/ThreadSafety. To be thread safe, code has to make proper use of General/MemoryManagement in order to be certain that there are no references to objects which have been deallocated, and no releasing of objects by something that does not own them.

In order to preserve General/ThreadSafety, you can send everything you use a retain message, you can employ an General/AutoReleasePool and General/AutoRelease objects, or you can use the concept of General/WeakReferences to avoid General/RetainCycles.

* **Locking objects: **
Any time more than one thread accesses the same piece of data, that data must have some sort of lock applied to it, to prevent its value from being changed by one thread behind the back of another thread.  See General/NSLock and its variants for tools to use in this situation. http://goo.gl/General/OeSCu

**see also: ** General/CanTwoThreadsLockFocusOnTheSameView

* **Thread safety of Apple's classes: **
I have heard conflicting reports about the thread safety of the General/AppKit -- specifically, whether or not General/AppKit classes can be called from any thread other than the main thread.  Some people have emphatically told me that it should not be done.  However Apple's own documentation (/Developer/ADC Reference Library/documentation/Cocoa/Conceptual/Multithreading/Multithreading.html) suggests that is not necessarily true, although it remains somewhat ambiguous about what is and isn't possible.

I know for a fact that updating an General/NSTextView from a worker thread is unsafe. I know for a fact that working with icons (via General/NSWorkspace) in a worker is unsafe. I strongly suspect all menu operations are unsafe. Pending more complete documentation, I'd assume everything in General/AppKit is unsafe unless told otherwise (such as drawing and other topics touched on in the release note).

http://developer.apple.com/documentation/Cocoa/Conceptual/Multithreading/articles/General/CocoaSafety.html

http://developer.apple.com/documentation/Cocoa/Conceptual/Multithreading/articles/General/CarbonSafety.html

* **Inter-Thread communication via General/NSConnection: **
You can use General/NSConnection to communicate across threads safely. I have found this to be really convenient, but with some performance impact. But it's worth a try.

Find some trivial examples in:

/Developer/Documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/General/NSConnection.html

You can use its code snippets to implement a simple client/server that uses mach messages to pass messages. To aid development, build the server into an Application (so General/AppKit manages the run loop for you) and create a command-line tool client or an Application client, whichever is easier. That should give you a leg-up on writing us some sample code ;-).

When making your app thread-safe, General/NSConnection.html again suggests using mach-ports, and illustrates exactly how to do it. I was able to just plug it into an existing project of mine.

There are some less-elementary examples of using DO on TCP/IP on the system here:

/Developer/Examples/Foundation/

See also

/Developer/Examples/Foundation/General/MultitThreadedDO


Thomas Lachand-Robert also wrote a whole page about communication between threads, and in particular to General/AppKit:
http://lachand.free.fr/cocoa/Threads.html
Don't hesitate to send him any suggestions for additions or changes.



----

I also added some General/DistributedObjectsSampleCode from the General/MacOsxDev list.

-- General/StevenFrank





----

This recent post from General/MacOsxDev may also be of interest:
    
From: Toby Paterson <tpater@mac.com>
Subject: Inter-thread messaging: a solution

The topic of how to communicate between two threads efficiently has come 
up a number of times while I've been on this list.  I've written some 
code to facilitate this and am making it available for your use and 
abuse.

The model expects that you have one or more worker threads that need to 
post information back to the main, UI thread, though it can be used to 
communicate with any thread running a run loop.  It takes the form of 
categories on General/NSObject and General/NSNotificationCenter to allow you to invoke 
methods and post notifications in a specific thread:

     [master performSelector:@selector(workerDone:)
             withObject:self
             inThread:mainThread];

     General/[[NSNotificationCenter defaultCenter]
         postNotificationName:kSomethingHappened
         object:self
         inThread:mainThread
         beforeDate:General/[NSDate distantFuture]];

The implementation uses General/NSPortMessages to marshall the message to the 
target thread.  It's more light-weight than DO - General/NSPortMessage and 
General/NSPort are a fairly thin veneer on top of mach messaging - but not 
nearly as general.  Make sure you read the comments in the header for 
some caveats of the argument marshalling.

You can download it from:   
http://homepage.mac.com/tpater/General/InterThreadMessaging.dmg

I'm releasing this code in to the public domain: use it; modify it; ship 
it in free or commercial software; just don't expect any support nor 
assign to me any blame if using this code causes you any mental or 
physical harm.  Have fun.


-- General/StevenFrank

The above page is no longer up, but i have mirrored the code. See General/InterThreadMessaging. -- General/DustinVoss

----

I have made a utility class, General/FDObjectiveMethod, that allows you to treat a method as an object. You initialize it with a complete (and that part is important, because if it's incomplete, you'll get errors) General/NSInvocation, and then start it with the -execute method. You can halt it part way through with the -stopExecution method.

It runs the invocation in a separate thread, so you don't have to worry about that.

It's here on General/CocoaDev, on the General/ObjectiveMethod page.

-- General/RobRix

(It's also a better idea to use General/ThreadWorker. General/FDObjectiveMethod has fallen by the wayside. -- General/RobRix)

----

Just starting some rewriting. -- General/KritTer
