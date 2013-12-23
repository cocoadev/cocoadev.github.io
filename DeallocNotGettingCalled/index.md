---
layout: page
title: DeallocNotGettingCalled
---

I seem to have forgotten something about the use of dealloc and it is not coming to mind.

I have an object which is instantiated in the nib file of a non-document-based app, and its dealloc is not getting called
when I terminate the app. I thought dealloc was supposed to be where you had a chance to release any
leftover objects that your class owns. I know it gets called when you can release an object programmatically, but what's going on here?

Log messages placed in the dealloc method are not appearing in the console window, and I cannot stop the debugger on any line in the method.

What am I forgetting?

----

Dealloc is called when an objects retain count drops to zero (not necessarily when you release it - other objects may retain it).  If your object is never dealloced, that's because its retain count never reaches zero.  You have a memory leak, though very likely not an important one.  

This is probably worth tracking down, but in general in cocoa you should not count on ever receiving dealloc messages.  Any object in the entire system can extend the lifetime of one of your objects, so it's fragile.  Dealloc should only be used to deallocate memory, release referenced objects, and do other non-deal-breaking cleanup.

*I don't think that's right. With the way Unix manages memory, any memory in use by your application will be wiped after the application quits. This means that your objects don't need to be explicitly deallocated, because any objects they release will also be deallocated. In this way, you can't really leak memory outside of your application (which is good, because if you ran it some huge amount of times with a 1KB leak each time, however unlikely this is, you could actually start tying up the system). If you really wanted to do anything besides release other objects before quitting, you should look at     NSApplicationWillTerminateNotification. --JediKnil*

Yes Knil, the memory is not permanently gone.  It's still called a memory leak.  This is why I was unconcerned about this particular leak - if it's from MainMenu.nib it's probably not something leaks more and more memory over the lifetime of the application.  You will not receive a dealloc message when the system cleans up your leaked memory.

----

Yes, of course, but what troubles me is this: There *is* no other object to retain this instance. It's a controller for a one-window app.
The dealloc code is never encountered. I can't even get to it with the debugger.

The controller instance in my nib file is the one that never reaches dealloc. It's instantiated in the nib file (or rather, when the nib file is loaded by the app)
and there is not, in fact, any code where I can release the object. The concern is that none of the utility objects that the controller owns during its
lifetime can get deallocated if the dealloc method is never called by NSApp or whoever is responsible.

I can put all the cleanup code in applicationWillTerminate: and make the controller the delegate of File's Owner. So I can clean up.

But routinely (and in most tutorials) we are told that an object's dealloc method is your last chance to clean up your owned objects.

It now seems that objects instantiated in this way do not go through dealloc. Even a pristine project with an object instantiated in the nib
does not send dealloc to that object when the app shuts down. As pointed out above, this is unimportant since all dynamic memory
associated with the app is returned to the system at that time.

This seems to imply that if you *do* have some last minute archiving or synching to do, you have to do it in one of the delegate methods,
rather than in dealloc. It just does not seem to me that it has always been this way. If so, I think it is cleaner, actually.

Of course, classes like NSDocument still automatically call dealloc when a document window is closed.

----

As documented at [http://developer.apple.com/documentation/Cocoa/Conceptual/LoadingResources/Concepts/NibFileLoaded.html]
 **you** are responsible for releasing objects instantiated in your nibs. The runtime won't do it for you.

----

I read the above article and drew a different conclusion. The article states clearly that the File's Owner is responsible for
releasing the top level nib objects, such as my NSObject subclass. In this case that would be NSApplication by default.

All the rules of memory management I have read state that I am responsible for releasing all the objects that I create *inside* that
NSObject subclass. I had hoped to do that in the dealloc method of that subclass. But since NSApp is evidently *not* releasing the top
level nib objects it is responsible for as the nib file's owner, I am puzzled.

If I put the same code in an NSDocument subclass in a Document class nib, it will behave as it should. The File's Owner will take care of it.

----

The article says that NSWindowController will release nib-instantiated objects. NSApplication won't. You should not instantiate objects in nibs where FilesOwner isn't a NSWindowController, NSDocument (which uses a NSWindowController behind the scenes), or a custom class of your own making that takes care of this. Instantiating something in a nib is no different than instantiating it in code. It's still *you* that instantiated it, and it's still you that's responsible for releasing it.

----

The above respondent makes the surprising claim that one should not instantiate objects in nibs where File's Owner is not a NSWindowController.

----

Search cocoabuilder.com for "nib release instantiate". The topic comes up quite frequently. In particular, [http://www.cocoabuilder.com/archive/message/cocoa/2003/12/21/489] and [http://www.cocoabuilder.com/archive/message/cocoa/2004/1/14/98804] which talks about exactly these issues.

----

When the application is terminated some deallocs are not called to speed up quit process. If you need to do something other than release memory you should use applicationWillTerminate:

*As stated in the conversation above, it's not to 'speed up' any process, but this is due to a bug in the implementation of NSApplication which should release the objects when done. Of course, applicationWillTerminate: is a perfectly safe way to solve this problem, but this remains a NSApp bug nonetheless.*

Can you state why you think it's a bug? From where I sit, it doesn't look like a bug. When you're running on an operating system that explicitly guarantees it will free all memory used by your program when it quits, not deallocating everything before quitting is hardly buggy behavior.

*submit this one to Apple and see how fast it gets marked 'behaves correctly**

----

I am the person who started this thread, and I think that "all of the above" is most correct. One practical significance of the misbehavior
of NSApp is that you cannot sandbox a window controller for a document-based app in a non-document app and have it behave *just* as it
would if the app were document-based. This is a minor annoyance in the long run. I still contend that when I was first learning, I could
log from dealloc in a non-document app, but I have damaged or destroyed a lot of cerebral neurons since then.

----

The entire field of programming is filled with legions upon legions of quirks, special cases, obscure trivia, arcana, heavy wizardry, and all the rest. This is incredibly minor compared to most things, and definitely doesn't qualify as a bug in any conceivable way. It could certainly qualify as unexpected behavior, and maybe even bad behavior if you're in the proper frame of mind, but that is not the same thing as a bug.

In particular, nothing in the documentation given the least bit of expectation that this will happen, so it shouldn't be too incredibly shocking when it actually doesn't.

----

I have experienced the "dealloc not getting called" nightmare.. I am a newbie in the Apple Cocoa world.. So, I spent 3 hours trying to figure out what I was "doing wrong".. And guess what.. It wasn't me!

IMHO this is for sure a bug... At best a GROSS oversite on Apple's part.. I say this because as a PC developer learning Cocoa, I really expected this to work.. It is an obvious expectation. Several Apple code examples show this! 

Now to make matters worse, this appears to be a COMMON misconception.. I purchased 2 different books to help with my Cocoa adventure, and EACH of them shows examples of -(void)dealloc.. 

Memory management is a game of "put away your toys"... It appears NSApp goes to bed without picking up it's room<grin>..

Apple should fix this.. I don't buy the optimization argument either... REALLY we are expected to believe that? Come on... I have been programming for 25 years.. I am not quite that gullible anymore. 

If Apple wants to grow it's developer base, then these kinds of mysteries need to be solved. Object lifetime and clean-up should not be a riddle.

----
What leads people to believe this is a bug or oversight? The dealloc method is called if an object is ready to the deallocated during the program's lifetime. When a program exits, all the program's memory is released anyway, so there's no reason to call dealloc.

----
What leads people to believe this... is that IT IS A BUG!.. There is nothing else to call it.
Let's suppose that there is something OTHER THAN memory that the object is allocating or manipualting.. Let's say talking to an external device or managing some other critical system resource... 

How do you suggest that these resources get released or shutdown properly? Jerking the rung out from under an object is a bad plan.. It can lead to many stability and reliablity issues. There are many resources in a system, not just memory...

Objects MUST have defined lifetimes and be closed properly and reliably for software to work in the real world.
 
Respectfully submitted.. regards.. chrisaiken@mac.com

----
A whole lot of Java, Lisp, SmallTalk, and Python programmers were no doubt shocked to realize that none of the software they have ever written works in the real world. I'm sure they'll all port their software to environments that do work in the real world as soon as they can.

The name of the method is "dealloc", as in memory deallocation. It's not "cleanup". Relying on dealloc to free external resources is a bad idea. This is hardly unique to Cocoa. Java and other garbage-collected languages heavily discourage it since garbage collection is nondeterministic, and so there is no guarantee your external resource will ever get freed. Cocoa with autorelease pools are in a similar situation, so relying on dealloc to release your external resources is a poor idea.

Just because a system doesn't match your preconceptions doesn't make it bad. Sometimes different is bad, sometimes it's good, and sometimes it's just different. Programming is inherently difficult. Complaining about this one little difference that takes five seconds to understand and which occurs in many other programming environments is just ridiculous. It's different, this is how it is, other things do it to, move on. -- MikeAsh

----

Dealloc as in DEALLOC the object.. Class destruction is not a preconception... It is a reality, a rule, and a generally expected behaviour. 

My point is this.. If I create an instance of a class, it is my responsiblity to destroy that instance.. If I don't, I have created a leak.. It is my bad... I can't blame anyone else.

I have been programming for over 25 years, and never have I had anyone tell me that leaking resources was OK, or "just the way it is", or "everyone does it, so don't worry".. Dang.. I find that a little bit shocking! If I let my programming staff code that way, I would hate to think of the support calls we would have! I agree programming is difficult, but a few rules, consistently applied, sure make life easier.

The framework create the instance of this class, so IT is responsible to destroy it properly.. Do you really think that is a preconception? Therefore, I submit that this is the bug Apple should fix.. It isn't hard, and it would be the correct way to resolve this discussion. I would prevent everyone from jumping through hoops to try to accomplish a work-around, and hours of wasted time trying to justify a mistake.

Classes were designed to encapsulate code/memory/resource utilizations.. Simply, how is one to "clean-up" a class if the destructor mechanism is not called? 

Garbage collection, I agree is NON-DETERMINISTIC, but that is quite a different issue. This relates to WHEN an object is destroyed, not IF it will be..

For sure, I will move on.. Because until Apple fixes the issue, we must find some way to manage an object's lifetime. 

----
Nothing is being leaked in the standard case. The only time you will leak resources is if you incorrectly rely on dealloc to release external resources.

Your strawman attack annoys me greatly. I'm not saying you should leak resources. I'm saying that the way you're attempting to do things will leak resources in many systems, and this is why you shouldn't do things that way. Please don't act as though I've made statements which I have not.

Garbage collection is *not* a different issue. GC makes no guarantees that any one object will ever be destroyed. In fact, GC is worse; if your application runs for a long time, it can potentially continue to leak while it's running until you run out of resources, whereas with Cocoa the only time it can happen is when you quit.

Even if dealloc were called when the application terminated normally, it still won't catch crashes. So you would still leak in that case. Better to clean up properly.

The standard technique is to simply have a -close call that you invoke manually. If this disturbs you too much, have your objects listen for NSApplicationWillTerminate notifications.

Cocoa clearly doesn't conform to your expectations. Your problem is in insisting that this is an obvious bug. Yet the fact that we're arguing about it means that it's not so obvious. You should consider the possibility that there is more than one correct way to do things. -- MikeAsh

----

This is a non-issue. It's a quit-time optimization that the frameworks don't call dealloc on their objects. The OS will free the memory your app is using when it tears down your process, much faster than it could by walking the object trees and call dealloc. dealloc is ONLY used for freeing memory - not for releasing devices, locks or closing files etc. It is just the same in C++ frameworks - you should never do "real work" inside a destructor.

----
Actually, this is fairly common practice in C++. It's called RAII, or Resource Acquisition Is Initialization. It's generally done using stack objects since those always have very clearly defined lifetimes. The advantage is that the resource always gets freed even if an exception is thrown through the code or you call return in the middle of the block. (Of course it's not freed if somebody calls     exit() in the middle. However, without stack objects, RAII becomes much less useful. -- MikeAsh
----

Finally: Why do you need dealloc called for your objects when your app is quiting?

----
By the way, dealloc of all objects can only be done if your objects form something like a tree-structure, where releasing the top-level object will cause lower-level leafs to destroy. But remember that there are a lot of complex patterns in Cocoa applications, like:

* Singletons (which can never be released)
* NSBundle/CFBundle loadable code (NSBundle can't be unloaded)
* Objects instantiated from .nib files (Which have strange retain counts)
* Multiple relationships between objects (What object have rights to dealloc another object?)


How could you release all this stuff in the time of application termination, so any-object-ever-created will be freed? If you really want to dealloc to be called before application quits, just use this in your AppController class:

    
- (void)applicationWillTerminate:(NSNotification *)aNotification {
 [self release]; //or autorelease 
}


After fixing all SIGBUS'es and such, don't forget that secondary threads you create with NSThread can't be joined when you app quits, because Cocoa will just kill them inside of     terminate: method without notifications. So objects inside them will be never deallocated too. 

-- DenisGryzlov

----

To quote Apple's NSObject dealloc documentation:

"Note that when an application terminates, objects may not be sent a dealloc message since the process�s memory is automatically cleared on exit�it is more efficient simply to allow the operating system to clean up resources than to invoke all the memory management methods."

----

Guys.. Maybe I am living in a strange world.. So please help me understand the fault in my thinking and expectations.

An object by definition is an association of code and data. In many cases your code (in a class instance) allocates resources other than memory.. Let's say opening a file, opening a device, starting some kind of communication session, using some sparse operating system resource (e.g. a handle), or more normally creating/using other objects of which IT is responsible to release.  These other objects can also claim resources, of which as a creator/caller we are not privy to. This propagates to an unknown depth, since we don't know or have control of the implementations of someone else's objects..

Now how are we to properly terminate our objects/resources if we do NOT KNOW we are closing/dying?

Ok. Presume, every object could have a CLOSE method. These methods would have to be explicitly called by the caller.. It would be the same trouble as we have we -dealloc at the moment. If a caller is not going to release me, I doubt the caller will call my -CLOSE.. 

Ok. So we can hook "applicationWillTerminate". This I have done.. Seems to work.. This is weird to me.. Since I must explicitly be on guard.. As a EXTRA protection I could see.. But not as a normal case..

The reason I contend that the current method of managing is a BUG, is that this is REALLY an exception that has been somewhat managed by the OS, not REALLY the proper way to shutdown an objects. 

"Put away your toys" is a good basic rule. It should never be purposely violated.. Only an unmanagable expection should create this trouble.. Terminating an application is not normally considered an unmanagable exception case. If NSAPP simply released the objects it instansiated, then all would be well. 
 
The trouble is that since Cocoa doesn't manage it's top level objects correctly, it doesn't happen as it should, so we don't get a chance to behave properly.. Everyone is forced to first understand the fault, and then work-around it. 

To me it's an obvious bug in top level object managment.. clear and simple. I can't come up with a way to forgive this.

The advantages of proper clean-up greatly outweigh the "optimization" or speed increase on termination. Are you really willing to trade potential system leaks for a few millisecs gain on the death of an app or a thread? If I review someone's code, and they don't clean up their allocations, then I will send this back for correction with a kind reminder. 

Maybe I am a victim of my upbringing.. When system resources were expensive, and memory was sparse.. Or maybe it's my background in real-time control, where devices are precious, and communication is paramount.. 

Or maybe I am just a crazy guy.. (I am sure of it!)

So, If I am the only person in the world that thinks the Cocoa behaviour is strange.. Then I am doomed to be lonely in my old-age<grin>..

Regards..chrisaiken@mac.com

----

I think I can help. The fundamental problem you're having is that Cocoa does not work the way you want it to work. It's even documented specifically to work differently from how you think it should work (see the quote from NSObject's documentation posted above by some kind respondant). You can continue to think that it's wrong and you're right, but that doesn't change the way that it works. Perhaps, instead, we can help you change your mental model of things so that it comes a little closer to Cocoa's reality.

After reading all of the above, it seems that you're equating -dealloc with, say, C++'s object destructors. They're not the same thing, though. Consider the way that objects in Objective-C are created. First you call -alloc to allocate some memory for your new object, and then you call -init (or some variant of -init) to initialize your object. I'm sure you'll agree that except in the case of NSObject itself, it's an error to try to use an object before it has been initialized. Therefore, Objective-C's -alloc is not the same as C++'s constructor. It's similar with -dealloc... you should use it to clean up any memory that you've allocated, but that doesn't mean that it's exactly the same as a C++ destructor. -CS

----
Time to SignArguments, no doubt.

I'm a little confused as to your position now. You say "If a caller is not going to release me, I doubt the caller will call my -CLOSE." You seem to want the system to clean up leaks for you as well. This isn't going to happen. If you have code in your application which is misbehaving then you lose, period. There's no way to protect against other code running in your address space.

You say "Are you really willing to trade potential system leaks for a few millisecs gain on the death of an app or a thread?" But that's not the question at all! The question is *how* you ensure that these leaks do not occur. In whatever system you're used to (which is what, by the way?) apparently it's sufficient to put your cleanup solely in the object deallocation method. In Cocoa this is not the case. Cocoa does provide techniques to ensure these leaks don't happen. They are merely different techniques.

I did some research and discovered that Java doesn't (usually) invoke finalizers on application termination either. The documentation for     runFinalizersOnExit states:

*Deprecated. This method is inherently unsafe. It may result in finalizers being called on live objects while other threads are concurrently manipulating those objects, resulting in erratic behavior or deadlock.*

Therefore your statements about garbage collection are not correct for all systems. Not only does GC nondeterminism mean you don't know *when* an object will be destroyed, it also means you have no guarantee that any non-memory components of an object will *ever* be cleaned up, at least in Java. I would not be surprised if other GC systems were the same. Relying on finalizers to release limited external resources is a bad idea in any case, so providing an app-termination fallback isn't really helpful to them.

I'm curious as to exactly which sorts of system resources you're referring to when you're worried about leaks. A terminating application cannot leak memory, file handles, sockets, or non-distributed locks. It is possible to leak temporary files but it's also frequently possible to limit the window of death in those cases and ensure that they're always cleaned up when you exit. There are always exceptional circumstances but they're rare. In any case, anything which the OS cannot clean up will not be cleaned up if your application should crash, so the possibility of a leak is *always* there. Your application will not leak on termination unless you don't know how to do proper termination cleanup with Cocoa, and if you don't know how to do that then the leaks are your own fault, plain and simple. -- MikeAsh

----

Mike.. Here's a bit of background to provide a context for my gripe.

I have for years worked in both the PC and embedded worlds. On the PC side, I have written mostly in a conbimation of Visual C/C++ (for device drivers and near real-time/multi-thread OCX/ATL controls), and Visual Basic (for User Interface/Database portions). On the embedded side, this has been mostly C/C++ with a range of compilers (e.g ARM, DSP...). I have never embraced Java, due mostly to performance issues.. so I haven't had any real experience in the field of garbage collection.. I have had to be responsible to take out my own trash.

My work has been focused mainly in high-speed vision systems and optimization lines for the solid wood industry. 

Life has taught me to "free if thou malloc...delete if thou new".. So to me it is natural to expect that this is a basic premise..

About 6 months ago, I bought a IMac.. First off, I love it.. Because my son and I are tinkering with HD video, and there is almost no better platform at the price. With this IMac sitting at home, I began to play with Interface Builder, and XCode, and Cocoa.. 

I was especially interested in Cocoa, since I bought and installed many years ago, NextStep! The promise of NextStep in those days was a fully implemented OO Operating System... I dream even today of such a wonderful life!

Now, take a case of an OCX/ATL control written in Visual C++, and called by the Visual Basic framework. 

- When an app creates an instance of the control it's C++ constructor is called.. 
- Also a method FinalConstruct is called.. (Here we do resource allocation.. Dynamic memory alloc, system resources (like handles to Windows resources.. ports...), spin up worker threads.. etc..)
- Now we work...
- When the control instance is destroyed by the framework FinalRelease is called.. (Here we release our allocations, shutdown threads.. clean up our stuff).
- The framework destroys the control/object.. the C++ destructor is called..

The above is repeatable and reliable.. ONLY in the case that some serious happens (e.g. an app exception.. a forced exit) does the wheel run off. 

%%QUOTE%%
I'm a little confused as to your position now. You say "If a caller is not going to release me, I doubt the caller will call my -CLOSE." You seem to want the system to clean up leaks for you as well. This isn't going to happen. If you have code in your application which is misbehaving then you lose, period. There's no way to protect against other code running in your address space.
%%QUOTE%%

What I mean by this is that, say I have some resources I allocate in the -init phase of my objects life, where do I put the clean up/dealloc code? Surely I am expected to release what I allocate?

I am NOT asking the OS to clean my leaks.. I just want to know WHERE in the objects' lifetime, or calling sequence, I am supposed to do My JOB of cleaning up?

If -dealloc is not called, then where do I do this?

NSApp is the caller in the above... If it won't release me.. Then as an object I don't know I am dying.. 

From what I understand, the delegates applicationWillTerminate: or applicationShouldTerminate: can give me a chance for this. 

Therefore I have hooked the delegate applicationWillTerminate.. did a [self release], then place the code to release all I have created in the -dealloc method.

Maybe my base misconception is that a object I create is NOT easily portable OUTSIDE a single application. If so, then you are mostly correct. I would normally be responsible for both the CALLER and the CALLEE, so I would ALWAYS be at fault.. But with top level objects (from the NIB), even in a single app, I don't have control of the caller. 

Is it possible to create an object and share this amongst many apps, or with many developers, like in the case of a COM object?

My surprise with Cocoa was that it did NOT destroy my object. I really expected it.. I do believe I should expect this. It seems MANY other people believe it to.. 

I purchased 2 different books on Cocoa development, and EACH shows EXACTLY that method of construction/destruction..(using -init and -dealloc). There is NO mention of applicationWillTerminate.. 

To conclude.. And really we must.. 

You believe this behaviour is ok. I do not. 

I probably won't change your mind, and you probably won't change mine.BUT that's ok.

What this discussion will hopefully do, is to highlight to others that may be coming from a like background to take care on this point. It is an easy assumption. 

If Apple is not considering this a bug, or an anomoly, it would be REALLY nice to make this point VERY CLEAR. Being new to Cocoa, I spent an evening trying to understand what I was doing wrong.. It could have easily been avoided with a few well placed words. 

Regards.

Chris Aiken (chrisaiken@mac.com)
President
Control Logic Inc.
www.controllogic.com
www.scanimation.com
www.lumberware.com

----
So basically, you don't have any *actual* problems, you just want all of your objects to be deallocated because it feels cleaner?

In most systems, objects that live for the lifetime of the application don't need to be destroyed and this is considered acceptable. If you're writing code it's good to code for destruction as well because you never know if you will change the code later on so that it no longer lives for the lifetime of the application. But in the case where Cocoa *knows* that the objects live for the lifetime of the application, it's entirely acceptable for it not to destroy them.

As far as making the point "VERY CLEAR", I really don't know how you could get any clearer than stating in the     dealloc documentation, "Note that when an application terminates, objects may not be sent a dealloc message since the process�s memory is automatically cleared on exit�it is more efficient simply to allow the operating system to clean up resources than to invoke all the memory management methods." -- MikeAsh

----
Hrm.  OK, well here's an question for you.  I spin a thread that is updating an local sqlite db instance.  If the user exits my app, I need to ensure that all classes using that singleton instance shutdown/go away and that afterwards, I can uninitialize my access to the db.  How do I do this without implementing some giant custom chain of finalization calls if there is no guaranteed way to dealloc or deconstruct my objects?  Yes, I'm very used to the c++ way of doing things, so what's the cocoa way of doing this? -- rkuo

----
Use the -applicationWillTerminate: delegate method on NSApp. In Adium's, for example, we wait on an NSOperationQueue to ensure that all background threads are done writing out files to disk. -- Catfish_Man

----
What if it's a command-line tool?  No nib.  No NSApp.  Where is the "correct" place to put this code? ---

Huh..  http://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/GarbageCollection/Articles/gcFinalize.html#//apple_ref/doc/uid/TP40002453

Apparently there is a "finalize" method of NSObject if you use GC.  Still, they recommend you just create your own method.  The few places I can find on ObjC "standards" for this say that it is typical to create a "free" method to handle any resource issues.

