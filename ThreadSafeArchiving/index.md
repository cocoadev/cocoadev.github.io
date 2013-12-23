---
layout: page
title: ThreadSafeArchiving
---

I have a large General/NSArray which is made up of several custom class objects. I occasionally archive the array (using General/NSKeyedArchiver) then save it to a file. Archiving the array takes several seconds and therefore halts the UI. As a result, I want to handle the archiving in a separate thread.

What is the best way to do this? Is it sufficient enough to simply make the accessor methods thread-safe in my custom class? Are there any unexpected thread-safety issues when using General/NSKeyedArchiver?

-- General/RyanBates (who has very little experience with threaded programming)

----

The principle of threaded programming is simple; while thread A is doing something, thread B can do *anything*. As always, the devil is in the details. So imagine one potential problem with a straight-up threaded archive: your archiver thread is sitting on object X, and object X tells the archiver to encode object Y. The archiver gets object Y and is about to retain it and archive it when it's your main thread's turn on the processor. Your main thread changes X's object and releases Y, causing it to be deallocated. Your archiver thread restarts and explodes upon accessing the deallocated object. There are probable lots of similar problems you'll run into with concurrent access to a shared data structure like that. Making all of the accessor methods thread safe *should* be good enough, but it might be hard to get all the gotchas. And then you'll end up with bugs that can only be reproduced every second Wednesday on dual-processor G4s.... There are two ways I can see to do what you want to do.

1) The threaded way. Make a deep copy of your entire data structure. This will probably be pretty fast. At that point, you can archive the copy without any issues.

2) The nonthreaded way. Make your archive process fast enough that you don't need threads. General/NSKeyedArchiver is really slow. You can speed it up by avoiding certain behaviors. For example, the current implementation is very slow if you are encoding lots of arrays. Encode the arrays as separate objects if possible, and you might get a significant speedup. Alternatively, use a different archiver. General/NSArchiver is quite a bit faster than General/NSKeyedArchiver, and (shameless plug) General/MAKeyedArchiver is an order of magnitude faster and it still gives you keyed archiving.

-- General/MikeAsh

----

Referring to point 1, how would you make a deep copy of an array? The only limit-less deep copy solution I've found is by archiving then unarchiving the array, but that will not solve the initial problem. That solution was mentioned here: General/MutableDeepCopyAndUserDefaults. -- General/RyanBates

UPDATE: Oh! Nevermind, it appears that calling "copy" on an array does do a deep copy. I will try that then. -- General/RyanBates

Okay, I was wrong. The "copy" method **doesn't** copy the contents of the array, it only copies the pointers to the objects in the array. I need to use General/NSArray's "initWithArray:copyItems:" method to get a deep copy. So far this is working very well - it only takes a split second where archiving that same array takes several seconds. Thanks for your suggestions Mike! -- General/RyanBates
