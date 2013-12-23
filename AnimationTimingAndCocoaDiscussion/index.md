---
layout: page
title: AnimationTimingAndCocoaDiscussion
---

*My parts in this discussion are those of a person who was just beginning to learn to code; view this discussion as a learning-experience-in-progress from several years ago. -  JoeOsborn*

Most of this page references a former revision of an article which suggested many poor programming practices.  Mike and others were very right- NSTimer is perfectly suitable for games. I cannot recommend any approach to animation that requires while() loops.  The article has since been fixed.-- JoeOsborn

----

OK. My complaint with this code example is the use of an unmetered "while (1)". When you watch this program in top(1) you will see it consuming 80-100% of the CPU in order to do a mere 30fps animation. In general, this means the program is taking time away from other processes that could be using it (though not starving them, thanks to the kernel). In specific, on my TiBook 667 this means the fan is about to kick from "off" to "hyper overdrive" and my battery life is about to drop through the floor.

This is the hallmark of a poorly written Mac OS X program, most commonly seen in poor Carbon ports.

The good thing about NSTimer is your code will block on a kernel trap waiting for the right time to execute. When that time arrives, your program wakes up, does the work, and goes back to sleep again. Personally I disbelieve the arguments against NSTimer. I've used them to get reasonably good performance for hundreds of iterations per second. But, NSTimer isn't the only fish in the sea.

In fact, you could fix this while loop by adding something like:

    
 struct timeval tv;
 tv.tv_sec = 0;
 tv.tv_usec = 33333
 select(0,0,0,0, &tv);


to the top (or bottom) of the loop. The loop will do one unit of work, and then sleep for a few microsects until its time to do the next unit of work. Interestingly, the itIsTimeForNextFrame becomes unnecessary.

Or, if you hate and fear select(2) (which you shouldn't) you can also call a mach function:

    
 #include <mach/mach.h>
 thread_switch(THREAD_NULL, SWITCH_OPTION_DEPRESS, 33);


Rather than using NSTimer, you could also use performSelector:withObject:afterDelay: to string together animation methods. Adjust the "after delay" method each time to account for the time spent in your prepareForNextFrame.

I believe there's another interesting solution which involves getting the runloop to return "idle" events to the host application, presumably with some kind of granularity. Alas, I don't remember the details right now. Maybe someone else will fill this in.

Finally, why mess around with CoreServices on a Cocoa web site? You can get this value from the NSDate API:

    
 NSDate *start;
 NSTimeInterval elapsed;
 start = [NSDate date];
 < work >
 elapsed = [start timeIntervalSinceNow] * -1.0;


If you must program in C, why go any further than the system library?

    
 #import <sys/time.h>
 NSTimeInterval CurrentTime(void)
 {
     struct timeval tv;
     NSTimeInterval time;
     
     if (gettimeofday(&tv, NULL)) {
         NSLog(@"gettimeofday returned -1: %s", strerror(errno));
         return 0;
     }
         
     time = (NSTimeInterval)tv.tv_sec;
     time += (NSTimeInterval)tv.tv_usec / 1000000.0;
     
     return time;
 }


But again, I have a hard time believing NSTimer is all that bad -- it's certainly a **lot** better than melting people's laptops into slag. Code responsibly.

-- ("Mr Grumpy") MikeTrent

----

I'd like to insert for my defence that I can be quoted as saying: 
"Besides the above code being a poor idea, it will draw 30 frames every second.  Not too hard."
^^; It was a quick example of how to use a class.. but it's changed to something a bit more correct, I think.

I was also unfamiliar with the Mach functions... but the idea to use select(3) is a very good one, I'm going to try it Wednesday and see where it gets me.  I really have hated the while() loops' spinning constantly, and this will certainly cut down on that. I'll try it, thank you, and edit this page again.

I know how you feel about my timing techniques(they do seem to be silly, after all!), what about the rest of it? ^^;; Is it otherwise, in your estimation, a pretty decent tutorial?

--JoeOsborn

ps- An honest question since I can't understand the phrasing: What does this mean? "Because of the various input sources a typical run loop manages, the effective resolution of the time interval for an NSTimer is limited to on the order of 50-100 milliseconds." http://developer.apple.com/techpubs/macosx/Cocoa/TasksAndConcepts/ProgrammingTopics/Timers/Concepts/timerobjects.html  'resolution' is the word i'm struggling with.

----

It refers to the possible frequencies you can achieve. What it means, I guess, is that if you have an NSTimer set to fire every 25 milliseconds, it's not gonna fire as often as you'd like it to. Only about half that much at best, in fact.

Myself, I've been using MacOSX's GLUT implementation, which for interest's sake, is implemented on top of Cocoa, and other than the bug I discovered in glutTimerFunc() (anybody interested? :) ), I've found it performs admirably. And yes, it's using NSTimer in there. So I guess this is a YMMV issue; at the moment, my GLUT app is a lightweight, so I guess it might be referring more to apps like OmniWeb. With the amount of stuff its run loops have to handle, I'd not be surprised if their timers couldn't be very fine-grained. -- RobRix

----

I reimplemented all my animation using NSDate.  It's better, and the user of OSAnimationTimer only needs to call two methods ever(one to initialize, and one to sleepThreadUntilNextFrameDate).  The class is still useful, if only to refactor some common timing code out of a lot of different classes.  The examples above are changed too.  As usual, the Cocoa way is better. ^_^  It is a bit slower(the numbers I've been giving as framerates to the -init methods have to be doubled...), but it's much more pleasant to use.

I'll check out CocoaGL and GLUT and whatnot sometime...

--JoeOsborn

----

This is a good band-aid to fix the CPU utilization problem, and my powerbook thanks you.

But, while this kind of technique avoids using NSTimer, it also avoids going through the Cocoa run loop. Your program will be spending all of its time drawing and none of its time processing key strokes, listening for power off notifications, receiving apple events, noticing the user has resized the monitor, certain sound operations and so on. You're going to need to solve this problem.

This is the main reason why I advocate using NSTimer. It works with the NSRunLoop so you can write a normal Cocoa app (not Carbon, not GLUT) with some kind of animation. The other techniques above that work with NSRunLoop also integrate better into a Cocoa app.

Of course, you could always poll your run loop 30-60 times a second within your animation loop, but now we're back to that whole "poor application design" problem. Or you could use Carbon or GLUT event processing functions, but neither of these are really Cocoa. 

You could run your animation loop on a second thread, while the main thread handles all these mundane tasks. But even then, the second thread will want its own run loop. And now you have to worry about concurrency issues.

-- MikeTrent

----

My game doesn't animate 24/7, it only animates when a character moves- though I have noticed the tendency for keystrokes to line up... But it's not a bug, it's a feature! Type in a bunch of arrow keys and sit back as you get to where you're going.... eheh... Yeah, that's less than ideal.  But to achieve 32 frames per second, I'd need a timer that fires with an NSTimeInterval of .03125, and I've never been successful in pulling off a timer like that(Plus it does look pretty ugly to have     myAnimationMethod:(NSTimer*)extraneousArgument methods).  I tried the multithreading bit a couple of days ago, that didn't work out at all.  I don't know from multithreading. (I'm enjoying this discussion, though, this is what I live for. ^_^)

--JoeOsborn

----

I did some OpenGL work using NSTimer for a class at my university. I found that NSTimer seemed to perform a lot better outside of the main thread. The NSRunLoop associated with that thread didn't seem to have nearly as much to deal with, and ran a lot faster. Has anyone tried sampling the resolution/sec that an NSTimer in a thread that ISN'T controlling the AppKit has? I've seen 3d apps that do this correctly (that tweakwaves demo is a good example) so there HAS to be a reasonable solution to this problem out there.

-- DaveFayram

----

That's an interesting idea... I'll check it out after I finish my project.

--JoeOsborn

----

It's important to note when you do this, that there can be many problems with this approach, especially if you plan to do anything involved with OpenGL or AppKit. OpenGL ignores threading, so just access it from one thread and you'll be fine (you might want to put a lock around acquiring the conext just to be safe). Earlier Kritter put up an efficient locking design that would be useful here.

The Appkit though, that's a big problem. Don't make appkit calls from outside the main thread or you can get some very odd results. I've had the app just randomly crash, but then again I've also had the app make some very funny images the window widgets got mussed and the components drew incorrectly. 

-- DaveFayram

----

Second Opinion, formerly at article proper, now here:

"...while this kind of technique avoids using NSTimer, it also avoids going through the Cocoa run loop. Your program will be spending all of its time drawing and none of its time processing key strokes, listening for power off notifications, receiving apple events, noticing the user has resized the monitor, certain sound operations and so on. You're going to need to solve this problem.

This is the main reason why I advocate using NSTimer. It works with the NSRunLoop? so you can write a normal Cocoa app (not Carbon, not GLUT) with some kind of animation. The other techniques [detailed in AnimationTimingAndCocoaDiscussion] that work with NSRunLoop? also integrate better into a Cocoa app.

Of course, you could always poll your run loop 30-60 times a second within your animation loop, but now we're back to that whole "poor application design" problem. Or you could use Carbon or GLUT event processing functions, but neither of these are really Cocoa.

You could run your animation loop on a second thread, while the main thread handles all these mundane tasks. But even then, the second thread will want its own run loop. And now you have to worry about concurrency issues."--MikeTrent(full quote in AnimationTimingAndCocoaDiscussion)

(NSjoie note: the above code is capable of using NSRunloop; a project I'm working on(the code from which I modified for these examples) uses NSRunloop for input with the normal -keyDown: mechanism.)

----

It looks like Apple is making some efforts in this area.... See NSAnimation and NSViewAnimation, both added in 10.4 .... 
I don't know about the rest of you, but I kind of wanted an easy way to do Quicksilver-like animations, and finding those articles in Apple's dev docs made me happy.

----

There is the important issue of where your animation data is coming from. I get data from coreaudio in realtime, and use it to animate level meters and oscilloscopes. I can't just do cocoa calls from inside the realtime context, so I have to queue the data and have a non-realtime thread courier the data to the main cocoa thread where the level view class gets a     setLevels:(id)lvls;. Furthermore I get between ~80 to ~800 of these messages per second, depending on how coreaudio is configured, so I have to filter it down to 25-35 fps myself.

A situation like this might also arise with a screen saver, or even an online game. I use the mach semaphore because the mach semaphore_signal is guaranteed not to block, and thus realtime threads may message this timer loop.

In a separate thread:
     
 while(1) // Don't get angry!
 {
     while((semaphore_timedwait(_inputSem, _defaultTimeout)) 
     { 
         // Do idle work here, check for exit conditions, etc...
     }
 
     message msg;
     if(!fifo_get(_inputFifo, &msg))
         continue; // or return
 
     long double now = get_time_stamp();
     if(now - _lastTime < _minInterval)
     {
         // Not time to work yet, queue msg and continue.
     }
     else {
         // This part of the code runs at whatever frequency we specify, usually 30x per second is enough for animation, 
         // but I can lower it to, say, 10-15x per second if I want to save CPU for my audio processing.
         _lastTime = now;
         NSAutoreleasePool * arp = NSAutoreleasePool alloc] init];
     
         // Handle queued messages, etc...
         [client performSelectorOnMainThread:@selector(animate:) 
             withObject:[NSData dataWithBytes:&msg length:sizeof(message)] waitUntilDone:NO];
 
         [arp release];
     }
 }

- Jeremy Jurksztowicz

----
You should check out [[CocoaSpriteKit (http://csk.sf.net) or SpriteMagic (http://svn.afternight.org/vinay/SpriteMagic).  I use timed animation in both of those.  SpriteMagic is a newer codebase that I've been working on.  The animation works pretty much the same in both of those though.  NSTimers can fire off really really really fast.  I actually use 0 as the time interval, so that it fires off as fast as possible.  In the animation function (the fucntion that the timer calls) I can check if enough time has passed and then return.

In any case, feel free to contact me at vinay at (the domain of the svn server mentioned above)

----

One thing comes to mind that may be obvious to some, but doesn't seem to be mentioned here, so I will. If you are animating something on screen, there is no point exceeding a framerate of about 30fps - it won't get any smoother. So if you are animating an object that moves a certain distance in a certain time, you need to calculate the distance moved based on the actual elapsed time since the previous frame - don't count frames and assume this is equivalent. It might very well work on a high-end machine, but slow down on a slow one. By calculating the distance based on the actual elapsed time, the motion gracefully degrades on a slow machine by dropping frames, rather than going into "slow-mo". A useful side-effect of this approach is that you only need one timer to drive everything at a fixed rate of ~30fps, and then each animated object calculates its position (or sprite frame) by looking at the actual elapsed time and deciding where it's got to. Everything stays in the proper relative position to everything else and it automatically degrades gracefully. With multiple timers and framecounting (as I have seen in some designs) this is not guaranteed.  --GrahamCox

