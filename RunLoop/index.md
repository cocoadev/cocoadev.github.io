---
layout: page
title: RunLoop
---



Run Loop conceptual docs: http://developer.apple.com/documentation/CoreFoundation/Conceptual/CFRunLoops/index.html

Class documentation http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSRunLoop_Class/index.html

The RunLoop is what keeps your app running until it should quit. It handles events, et cetera.

If your application is made with the AppKit (and therefore uses NSApplication), you don't generally need to mess around with this, but if you are building, for instance, a Foundation Tool, you will need to set this up yourself if you need it. Foundation tools commonly need run loops in order to process networking events through a standard event mechanism, including using DistributedObjects with MachMessages or TCP/IP.

Look at the documentation for NSRunLoop, specifically the -currentRunLoop and -run methods.

According to Aaron Hillegass, you can use the NSRunLoop in some cases instead of having to use threads or something else more complicated. Run loops are relatively simpler when it comes to doing things with NSTimers and whatnot. I found a benefit when it solved a problem I was having with NSTimer. See NSTimerDoesntRunWhenMenuClicked.

----

All known run loop modes:


*NSDefaultRunLoopMode
*NSEventTrackingRunLoopMode
*NSModalPanelRunLoopMode
*NSConnectionReplyMode


----

I've noticed that apps sometimes create additional runloops.  Why would I need more than one?

----

I've only created additional runloops when spawning additional worker threads. When worker threads want to respond to events (again, like DO notifications, timers, or other notifications) they need to have a runloop running on that thread. 

-- MikeTrent

----

The documentation says a run-loop will exit when all input sources have been removed. Good luck getting that to happen. The best way to terminate a run-loop on demand is to run it for a brief time only with **runUntilDate:** and then check a flag. --DustinVoss

----

Agreed you can't always rely on the run loop exiting; but simply checking the escape condition at a fixed interval is a bad solution for two reasons:


*Picking an interval too low causes high CPU usage akin to a spin lock
*Picking an interval too high causes your task to live on longer than it should


Instead you should run the loop unconditionally with     CFRunLoopRun(). You then detect your task's transition into the completed state and call     CFRunLoopStop() which will cause the call to the run function to return.

If you eliminate the polling your thread is free to sleep in a system trap.

--KeithDuncan

----
I've tested this and it is correct that using     CFRunLoopRun() is much more efficient than a while loop like this:

    
 while (shouldKeepRunning && NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:[NSDate distantFuture);


It is very odd that the Apple NSRunLoop Class Reference documentation suggests the latter as that causes very high CPU utilization.

-RH
----
RH: This does not cause a problem because the -runMode:beforeDate: method has to return before the loop can continue. Because it has to was for the run loop to execute once, it's no more CPU intensive than -run would be.

-SF
----

My documentation says: *The current thread's run loop runs in the default mode until the run loop is stopped with CFRunLoopStop or all the sources and timers are removed from the default run loop mode.* But it does not seem to be true.

Does anyone know any way to terminate a running/waiting run loop?

----

Well, if you are managing the top-level of the loop yourself, you can use the *runOnce* (or whatever it is called) and check a termination flag after each time that returns in a  basic little while loop.

----

I don't see such function neither for NSRunLoop or CFRunLoop. Maybe you're thinking of     nextEventMatchingMask:untilDate:inMode:dequeue:?

*CFRunLoopRunInMode( kCFRunLoopDefaultMode, 0.0, YES ) would be analogous to NSRunLoop currentRunLoop] runOnce], if it existed.*

----

That's basically polling though, right?  Isn't there a more elegant way?

Most 'more elegant' ways will ultimately use polling deep down. Polling is the heart of multithreaded programming, use a semaphore for efficient sleeping and signalling.

*No, polling is where you constantly check for a value without sleeping, or sleeping for a constant time. If your thread sleeps continuously until the next event happens, that's not polling.*

Semaphores work with kernel timers queues and... polling.

*That still doesn't make polling "the heart of multithreaded programming". You never see the kernel polling its run queue on a timer interrupt, and you certainly aren't writing that code yourself unless you're hacking the kernel. **Your app** should not be polling, regardless.*

A kernel does not use polling to implement a semaphore. It's pretty obvious when one thread increments the semaphore, and at that time, the kernel can schedule the sleeping thread(s). Jon H.

----

Polling should be understood, if not used, by all serious programmers. Take a look at X11 server, it uses polling extensively, and extremely efficient for what it does. The implementers, previously tried using mutexes, only to find that the overhead was too much. To say that an application should never poll, is absurd. There are many cases where you simply MUST poll, in order to avoid priority inversion for example. That being said, writing polling code is very error prone (at least the first few times), and should only be used where other techniques do not fit within constraints.

----

Do you have anything to back up your statement about X11? I have X11 running right now, with nothing happening in it, and it's using 0.0% CPU according to top. I see no reason why X11 should have to poll for anything, so I have a hard time believing you.

Priority inversion can be avoided through non-polling techniques, such as priority inheritance. In fact, I'm having a hard time seeing how polling can help you avoid priority inversion in the first place. Can you elaborate?

As far as I'm aware, the only time an application MUST poll is when it needs to be notified of a change and the operating system provides no mechanism for notifying applications of the change. Modifications by other clients to files on a remote file server is one example. I can't immediately think of any others. For the vast majority of cases, there is a way to get the OS to notify you, so that your program can properly block instead of polling. Just like goto, there is the occasional rare case where it's necessary, but that doesn't mean it should not be discouraged.

*Spend a few minutes researching the X11 system and you will see it what I mean. The historical addendum concerning trying a synchronous approach first, came from an a recent article by P.J. Plauger (sorry If I butchered the name!) in an issue of Dr Dobbs, err... C/C++ journal(??), Lemme take a look for it.*

I tried, but I couldn't find anything like what you're referring to. I'd love some concrete material, as you've made me all curious now.

*Polling is also heavily used in Realtime programming, where priority inheritance is not desirable. My main point in all this, one which I unfortunately must make regularly on mailing lists, is that there are multiple environments in which programs must run, this by necessity requires us to have multiple, often colliding, techniques to handle these environments. To dismiss polling wholesale, is foolish, because you may find yourself in a situation where polling is the best solution to the problem and you have thrown it out of your toolchest. Sometimes I go on comp.lang.c++ and post a question about custom memory management, or some other arcane art, and the derogatory remarks just smack me in the face. Patronizing remarks like, "profile before replacing the memory allocator or you are wasting your time", or "polling is unsafe, use API xyz instead" just make me spin.*

If there is an API that lets you avoid polling, it's almost always better to use it, though.

*Anyways, I hope I did not come off sounding hostile, it was not my intent. I just remember learning realtime programming was so hard because I was forced to use techniques which were always portrayed as 'invalid' or superceded with better techniques. This process of unlearning would have been so much easier if I had understood from the get-go that some techniques are only 'invalid' in a particular programming context. As a newbie I equated 'application programming' with 'programming', much to my disadvantage when learning realtime programming. End Rant*
--[[JeremyJurksztowicz

Oh no, I read nothing hostile into anything. One should always remember that many "X is bad" statements are domain-specific. Here, the domain is programming for modern UNIX-alikes. When you get into vastly different environments, the proper techniques change, no doubt.

"Okay, I don't have 'anecdotal evidence', but have written two different real time kernels for microprocessors. First, polling was not used in EITHER of them. The person who said that polling is at the heart of the kernel is a) ignorant of what they are talking about or b) has been listening to others who are ignorant about what they are talking about. These kernels had locks, queues, mutexes, and NONE sat in a polling loop - I couldn't. One was a robotics system that had to respond quickly to events. Not sit around waiting for a loop to poll. The reality is (sorry to burst folks bubble) but an operating system that is written well will BLOCK (no polls, it stops execution) until something happens. This is how you block - you put the process on what is called an idle list. Then you indicate in its process block it is blocking on XYZ item. This allows some OTHER thread to run. Since it is in the idle list, IT NEVER RUNS. This is the important part. Later, another thread will change XYZ (whether it is a queue, a message box, a pipe, you name it) and the act of doing this TAKES the process off of the idle list and puts it back in the run list. Next time there is a context switch (when you switch which process is running) the now UNBLOCKED process will run. Bottom line: polling bad, blocking good. There is a BIG HOWEVER in all of this. HOWEVER there are times when you don't need blocking (it is an expensive operation for VERY short time periods). As for X11 and the whole mutex arguement... whatever. I've seen folks who didn't understand blocking screw it up badly (trust me, its easy). If X11 uses TONS of shared variables, yes, mutexes can make it run slowly - you can't just 'add stuff'. That's like saying 'I wrote something on Windows, tuned it and it worked perfect, but the port to OS X didn't work so well, therefore OS X is lousy'. Understand? "
--LloydSargent

*Traditionally, many DSP developers have structured their software applications around sequential processing loops and state machines.* - from the Texas Instruments DSP Design page. Is that polling?

As far as I understand the terminology, polling doesn't mean you have to use 100% CPU time to wait for a status change. You can very well relinquish control by yielding or adding a short delay after every status check. The technique that consumes all system resources is called spinlocking, and even that can make sense sometimes, as stated above. -GregorRiepl

----

If you are only blocking for a short period of time, an OS semaphore (or anything that requires a context switch) is often painfully heavy-weight.  Many efficient semaphore implementations will spin while polling for a short time and then call the kernel to block if they are still waiting.

That said, this is not spinning.  Running the loop once means *block for one event*.  So, this approach is reasonable and is just like decoding the event which is what runloop does, as well.  It is essentially just manually adding an *event* which the loop should behave like it implements.

"I find many people HIGHLY over-rate there piece of code and vastly under-rate everyone elses. Many times you can block those 'short periods of time' with little or no ill consequence - context switching is a lot faster than it was 20 years ago. Folks, like me, who write that that level are rare. For an app writer to say 'context switches are expensive' are quoting an old paradigm. It has shifted. Heck, if time is of consequence to you, bail out on Objective C - that sucker eats it on EVERY object!" -- LloydSargent

----

"Does anyone know any way to terminate a running/waiting run loop?"

I have successfully used 

     CFRunLoop();

Then later called 

     CFRunLoopStop ( CFRunLoopGetCurrent() );

And it indeed returns control to the code that started the run loop.  The following, however, does not work for me:

     NSRunLoop currentRunLoop] run]

Then later calling

     CFRunLoopStop ( [[NSRunLoop currentRunLoop] getCFRunLoop] );

I don't know what ill effects, if any, there are to bypassing the [[NSRunLoop way of starting the run loop for Cocoa apps.

----

This is the code I have always used when starting and stopping run loops in background threads (attaching a one second repeating timer):

    
 -(void)backgroundThreadStarted {
   NSAutoreleasePool* thePool = NSAutoreleasePool alloc] init];
 
   // create a scheduled timer
   [NSTimer scheduledTimerWithTimeInterval:1.0 target:self selector:@selector(backgroundThreadFire:) userInfo:nil repeats:YES];
   
   m_isBackgroundThreadToTerminate = NO;
 
   // create the runloop
   double resolution = 300.0;
   BOOL isRunning;
   do {
     // run the loop!
     NSDate* theNextDate = [NSDate dateWithTimeIntervalSinceNow:resolution]; 
     isRunning = [[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:theNextDate]; 
     // occasionally re-create the autorelease pool whilst program is running
     [thePool release];
     thePool = [[NSAutoreleasePool alloc] init];            
   } while(isRunning==YES && m_isBackgroundThreadToTerminate==NO);
   
   [thePool release];
 }
 
 -(void)backgroundThreadFire:(id)sender {
   // do repeated work every one second here
 
   // when thread is to terminate, call [self backgroundThreadTerminate];
 
 }
 
 -(void)backgroundThreadTerminate {
   m_isBackgroundThreadToTerminate = YES;
   CFRunLoopStop([[NSRunLoop currentRunLoop] getCFRunLoop]);
 }


-[[DavidThorpe

----

@David:

You're creating the timer, but not attaching it to the thread. The above code should look something more like this:

    
 -(void)backgroundThreadStarted {
   NSAutoreleasePool* thePool = [[NSAutoreleasePool alloc] init];
 
   // create a scheduled timer
   NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:1.0 target:self selector:@selector(backgroundThreadFire:) userInfo:nil repeats:YES];
   [[NSRunLoop currentRunLoop] addTimer:timer forMode:NSDefaultRunLoopMode];
 
   m_isBackgroundThreadToTerminate = NO;
 
   // create the runloop
   double resolution = 300.0;
   BOOL isRunning;
   do {
     // run the loop!
     NSDate* theNextDate = [NSDate dateWithTimeIntervalSinceNow:resolution]; 
     isRunning = [[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:theNextDate]; 
     // occasionally re-create the autorelease pool whilst program is running
     [thePool release];
     thePool = [[NSAutoreleasePool alloc] init];            
   } while(isRunning==YES && m_isBackgroundThreadToTerminate==NO);
   
   [thePool release];
 }
 
 -(void)backgroundThreadFire:(id)sender {
   // do repeated work every one second here
 
   // when thread is to terminate, call [self backgroundThreadTerminate];
 
 }
 
 -(void)backgroundThreadTerminate {
   m_isBackgroundThreadToTerminate = YES;
   CFRunLoopStop([[NSRunLoop currentRunLoop] getCFRunLoop]);
 }


- Brendan

----

Actually, according to the docs, the scheduledTimerWithTimeInterval message automatically adds the timer to the current run loop.

