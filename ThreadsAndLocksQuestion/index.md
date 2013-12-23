---
layout: page
title: ThreadsAndLocksQuestion
---

My app calls a method to each of its plugins, and each plugin is assigned it's own thread to execute this method in. So 8-9 threads are created instantly and each plugin's code is going on at once. Each plugin is sent 2 objects, which it can then access and call methods from (one object is General/NSUserDefaults).

So when this is all going on, sometimes I will get random errors, and sometimes it works perfectly fine. The errors are always the same few, and many times it has something to do with the General/NSTask I am running (I have a few methods that call General/NSTask).

One random error is this:     *** General/NSTimer discarding exception 'General/NSInvalidArgumentException' (reason **** -General/[NSCFDictionary setObject:forKey:]: attempt to insert nil value') that raised during firing of timer with target 5126d80 and selector 'runPrimaryTask:' but where I think this is happening I check for nil strings everywhere... and since it only happens randomly, with exact same test data, it's hard to debug.

The documentation at file:///Developer/Documentation/Cocoa/Conceptual/Multithreading/Tasks/foundation.html says that General/NSTask is generally thread-unsafe. Is there any work around for this? Would this be causing the random errors? Would I need to use General/NSLock's each time each plugin accesses one of the objects it is sent that is shared?

The General/NSTask's I use repeatedly call a Perl script that does simple regex (i.e. strip html). But I do use General/NSTask's for parsing HTML, and those have not had problems (but they aren't called repeatedly).

--General/KevinWojniak
----
Yes, you will most likely need to synchronize access to your shared objects if they not currently thread safe.

----
Use the debugger, Luke!

You say you don't know where the exception is being thrown. Put a breakpoint on     -General/[NSException raise] and *find out*. That way you'll get a lot more information and won't have to be guessing about what's going wrong.
----
I've been testing this like crazy, and when I call the function for the first time when the app launches, it always works perfectly. But as I keep calling it in same process, it starts to get more and more errors. But if I relaunch the app and start over, it works fine. It's really starting to get to me... I'm slowly starting to find where a few of the errors are being  generated from but still don't why because first time it runs, it works perfectly.
----
General/NSUserDefaults isn't thread safe, you'll have to guard all usage of it.

----

I've figured it out. And I've learned a lot better how to catch exceptions. After implementing General/NSLock and NS_DURING/NS_HANDLER/NS_ENDHANDLER I've made it much more stable. Also, calling General/NSTask's launch and waitUntilExit using performSelectorOnMainThread... fixed some other crashing I had too. Weird.
