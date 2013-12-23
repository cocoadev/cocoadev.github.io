---
layout: page
title: DoesCoreDataObsoleteInstanceVariables
---



*"CoreData organizes the application's model layer into a set of defined in-memory data objects."*

Do you think that CoreData will replace instance variables in the future?

*Why would it? I don't think it would more than bindings or a good document model has.*

I mean that an object wouldn't need to keep information about itself within itself, it could just store it in the database. Then when it or another object wanted to retrieve that information, it could just do a database query. Probably would be too slow on current hardware, but maybe in the future? I dunno, just an idea.

*AFAIK and IMHO, CoreData is basically a generic object with the capability to represent a specific "object" (read, set of properties) with the Key-Value ideas. An NSDictionary has been able to do this for a while now, as any object is basically a collection of properties mapped to variable names. So if you used a normal subclass of NSObject, and created a master list of all objects in an archive ("set of defined in-memory data objects."), probably implemented like NSAutoreleasePool (N<nowiki/>SAutoarchivePool, perhaps?), you could get the same behavior by having the "master list" auto-update itself now and then, archiving and unarchiving from a predefined file. Sort of like NSUserDefaults does. I think the whole CoreData thing is a bit messy: almost anything that you can express as an NSDictionary or NSManagedObject can be expressed as a distinct object, with its own class. This allows the user to specify the actual variables normally, instead of using     valueForKey: all the time. Even better, the "managed objects" could manage themselves, which is (albeit debatably, if that's a word) the whole point of ObjectOrientedProgramming. So I hope that CoreData and bindings *don't* replace instance variables, as the latter are a lot easier to use. The only real advantage seems to be the "Prototype UI Generation", which (I wish/hope) could be done anyway with the other new features in Xcode 2.0. --JediKnil*


JediKnil seems to have missed almost the entire point of Core Data (and it would be useful to properly distinguish between "Core Data" and 'NSManagedObject'...).  Core Data is an "object graph management and persistence framework".  To address various issues:  First, you can use custom classes.  Second, if you wish you're perfectly free to use custom instance variables -- see for example http://developer.apple.com/documentation/Cocoa/Conceptual/CoreData/Articles/cdAccessorMethods.html.  Third, to suggest that "only real advantage seems to be the "Prototype UI Generation" is frankly asinine.  The framework provides -- for no code -- undo and redo, relationship management, validation, persistence...  Moreover (and this also addresses a previous suggestion -- "an object wouldn't need to keep information about itself within itself, it could just store it in the database. Then when it or another object wanted to retrieve that information, it could just do a database query."), Core Data provides an abstraction layer above the persistence layer.  The role of the managed object context is to provide a scratchpad where you can edit an object graph precisely without affecting the underlying store.  Only when you're happy with the results do you commit changes.  This abstraction allows for multiple concurrent modifications.  The framework also provides a number of models for conflict resolution if an attempt is made to save mutually incompatible modifications.  This is a lot more than can be achieved simply with an NSDictionary, or with direct read/write from/to a database...

----

Also, given that accessing an instance variable is a single load/store instruction, whereas doing a CoreData query is probably at least a hundred times slower, CoreData simply does not have the speed to replace instance variables *everywhere*. -- MikeAsh

*It probably isn't 100 times slower unless it has to access the disk.  If you read the latest ADC article, it's clear that CoreData stores necessarily have an in-memory representation - for one thing the on-disk rep isn't changed until you explicitly ask for it to be changed.  For XML and binary plist representations, the entire file is loaded at once, just like at present.  An SQLite store isn't loaded into memory in one big shot, but there still must be a memory cache.*

I disagree. If it were on-disk, the penalty would be more like a factor of a million, and a factor of 100 is very reasonable for in-memory CoreData access. Assume that it's in memory, and then take the best case for the instance variable, where the it's already available in the L1 cache. In this case, the load will take about two or three cycles from when it's issued to when the value is available to be used.

Now, take CoreData's absolute best case. Invoking     [self valueForKey:@"someKey"] involves an Objective-C message send, which is *already* 50 cycles. We're already a factor of 25 slower, and we haven't even *done* anything yet except handle the first phase of the dispatch. Now all you need is three more message sends, or other operations requiring equivalent time, and you're at a factor of 100 from the 2-cycle L1 cache load case. Even a simple lookup in an NSDictionary in the best case will use four message sends, namely the initial dispatch,     [obj hash],     [foundObj hash], and     [foundObj isEqual:obj].

Also, the above analysis is ignoring things like the fact that the compiler can cleverly place the load early, execute other things while the load executes, and then use the loaded value, which can push the effective penalty for the load down to 0 in ideal situations.

Objective-C message sends are fast for what they do, and compare very favorably with things like C function calls and C++ virtual method calls relative to what each is capable of, but they are slow when compared to single processor instructions that suck data out of fast cache. -- MikeAsh


Instance variable access in Core Data isn't just data access.  It must also tie in with the access notification (in particular, faulting) architecture, and for setters the change notification system.  Certainly raw access is faster, but you're not comparing like with like.  And you can also regard the cost of the overhead as part of the price you're paying for an extraordinary level of functionality...


----

It might be worth considering that NSManagedObject is smarter than you give it credit for. There are a number of implementations that you're not supposed to override (isEqual, hash, dealloc, valueForKey, etc), so there's probably some clever stuff going on in there. Also, the -fobjc-direct-dispatch build setting (new in Tiger) changes all the rules about how long dispatches take.

There's an ongoing balancing act between higher abstraction and performance. If performance was the only thing to consider, we'd never have seen something like Quartz. CoreData does not completely obselete instance variables, it just removes gobs of redudant code that maintains simple Foundation objects like strings and numbers. It doesn't remove you from needing an instance variable for a window or a view. -- ScottStevenson

Re efficiency, see also http://developer.apple.com/documentation/Cocoa/Conceptual/CoreData/Articles/cdFAQ.html#//apple_ref/doc/uid/TP40001802-DontLinkElementID_15.

