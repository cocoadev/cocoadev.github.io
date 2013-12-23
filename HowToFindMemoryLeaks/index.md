---
layout: page
title: HowToFindMemoryLeaks
---



Some tips and threaded discussion about using such tools as the *leaks* program and links to other DeveloperTools for the purpose at hand:



* Expand the disclosure triangle beside "Executables" in XCode and highlight your executable (In Xcode 2.5, double-click the executable and select the "Arguments" tab in the Info window. Be sure to add environment variables, not arguments.)
* The right pane shows the arguments you can pass your application at runtime.  Click the + below Environment Variables.  The new Name should be MallocStackLogging and the Value should be YES. Add an environment variable for MallocStackLoggingNoCompact=YES as well.
* Compile and run your application
* open terminal and run "ps ax | grep -i yourappname
* The first column in the row for your app is its Process ID (pid).  Make a mental note of it.
* type "leaks pid|more" in the terminal, where pid is the number of the Process ID
* You will get about a paragraph of information for each memory leak.  The first line will usually give you the class name of the object that is being leaked.  Make a note of it.
* Using enter or spacebar to scroll, scroll down to the end of the entry, where you will see a series of Objective-C class and method calls all jammed together.  Somewhere in the last 5 to 10 method calls you can usually spot the name of a method you wrote in a class you wrote or subclassed.
* Examine that method in XCode.
* see RetainReleaseTips for information on how to manage memory.  The basic rule is: If you alloc, retain or copy it, you need to release it.
* exercise the functionality of your app, checking back on leaks occasionally to see if it is leaking memory.
* When you get all the memory leaks out the leaks command should return 0 leaks.
*In addition to Leaks, there are ObjectAlloc and OmniObjectMeter.



NOTE: You should make sure you do not run leaks with NSZombieEnabled or it will look like everything is leaking!

-- TKinch

----

Since I like to have things blow up sooner rather than later, I've added the following method to my application delegate.

    
#ifndef NDEBUG
- (void)applicationWillTerminate:(NSNotification*)aNotification
{
   char cmd[32], buf[512];
   snprintf(cmd, sizeof(cmd), "/usr/bin/leaks %d", getpid());
   if(FILE* fp = popen(cmd, "r"))
   {
      while(size_t len = fread(buf, 1, sizeof(buf), fp))
         fwrite(buf, 1, len, stderr);
      pclose(fp);
   }
}
#endif


Unfortunately the system seems to leak, in particular, NSSavePanel leaks a lot of NSCFURLs and NSCFStrings and the "drag manager" leaks NSCFDates.  But if I also set the     MallocStackLogging environment variable, I get a stack dump, and thus it's easy to spot what's my mess, and what's the systems mess! :)

----
This may be new with Panther, I'm not sure, but you can also just type     leaks YourApplicationName, and it will work (assuming there's only one copy running). No need to fart around with pids if you're typing everything manually.

About the code to run     leaks on application termination is interesting, but wouldn't it be simpler to just use     system() instead of     popen()?

----

Yes, I guess popen() is just a habit -- though it does have the advantage of controlling where the output should go.

----

I have two applications, one named avenir and one named avenirOutliner.  I found that using "leaks avenir" didn't always get the right one (which is probably a bug in leaks).  The pid was the most reliable way to make sure I got the right app.  For most apps you probably shouldn't need to do that though.

-- TKinch

*Thanks for that hint, knowing that will probably save me a lot of trouble some day.*

----

I've been playing with leaks and unfortunately there seems to be a lot of things that it will not catch -- leaks in ObjectiveC are likely to happen due to circular retain counts, and leaks doesn't detect these. I also tested some other things, where I'm quite sure that I'd thrown away the pointer to the memory in question (like add'ing some new'ed object to a std::map which I then disposed), but leaks still did not detect it.

OTOH leaks does seem to report that my programs memory usage grows, despite the fact that it says there are zero leaks, and I have inspected the source rather carefully, and there should not be any leaks on my part.

But I am uncertain to how serious I should take it?

----

The documentation for leaks says that MallocDebug picks up certain things that leaks doesn't, but doesn't pick up some things leaks does. I don't know if MallocDebug picks up CircularRetains, but it seems to be more MacOSX-tuned. So using them to complement each other seems like the best policy.

*IIRC the only thing that leaks misses is leaked Carbon Handles, which is not likely to be a large problem in a Cocoa app. It will pick up circular leaked structures. There are many ways for your memory usage to grow without creating a leak. You could just be fragmenting your memory, which will increase the amount of VM you use without increasing the actual amount of allocated memory. You could be leaking memory without losing a reference to it, for example by adding objects to an array and never removing them, but never accessing them again either.*

----
Anyone have any suggestion on how to check for leaks **after** the process has run?     leaks is great for finding leaks in code, but can only be used while the process is running; and simply checking available memory before/after running the process won't give me info on where any leak would be from (like Cocoa classes for example). Is there a tool that tells you if all allocated memory has been release after a process runs?

----
OS X, like al Unix variants, assures that all allocated heap memory and all open file descriptors used by a process are returned to the operating system when the process terminates no matter why the process terminated.   There is no such thing as a "leak after a process runs."  However, POSIX semaphors, message queues, some forms of shared memory, and some forms of graphics card memory can all be leaked.  Specialized tools exist to find each of those types of leak.  See ipcrm, vmmap, shmat, glAreTexturesResident.

----

Also, NSZombieEnabled is quite a handy little method for diagnosing this kind of thing in-app.  Basically when an object is deallocated, instead of being freed, it's converted to a 'zombie' class, and will log when you try to send a message to it.  It's normally used for debugging extra deallocations, but it can just as easily be used to check if objects were freed as expected.-- Bo

----

I've noticed that changing colors using the color wheel in an NSColorWell (from the color picker) will cause RAM usage to go up repeatedly... is this some sort of Cache, is there any limit, can I stop it? Or am I missing something :-/ It seems to leak in all apps?

----

Turn on MallocHistoryLogging, use leaks, and see what you can track down.

----

First, see if it is actually leaking, then see if it is slowing your computer down, otherwise you might as well ignore.

----

I agree with the first half - if it is not leaking, there is nothing to fix.  If it is leaking, though, it should probably be fixed.  Users will use your app in the damnedest ways, and may leave it running for months.  If this causes it to gobble up megabytes, they will not be pleased.

Aside: Application developers need to think of their apps as the _second_ most important app the user has running.  If your app is the sine qua none of the user experience, then you might be forgiven a RAM leak or tacky attention getting bouncing.  If you think of some other app as the real reason the user is logged on, then you will be more likely to fix up the fine details.

I am not a fan of premature optimization, either for space or for time, until I see how an app runs in the wild.  I do want the app, though, to not waste resources.  Using needless RAM because I have not done a memory optimization pass is one thing.  Leaking RAM, so my app continually gobbles more without any benefit feels more egregious somehow.

ScottEllsworth

----

I believe that what I have are RetainCycles -- which means that MallocDebug isn't picking it up as a memory leak.  The number of objects in the autorelease pool however, is far too numerous to see if any of them will leak even after the pool releases each occurrence of every object.  Are there other tools out there that would catch something in memory if it isn't referenced by anything in the stack?  Does "leaks" do that kind of thing at all?

----

Well, the leaks man page claims it has a modes that can detect cycles by running     leaks -cycles, but since I've never used leaks, I can't vouch how well it works. -- Bo

----

leaks isn't showing my anything, except three leaks in the AppKit, which I couldn't care less about (Apple likes to "leak" (reuse) it's save and open panels).  What are general cycle leak hunting techniques?

At the very least, is it possible to get all the objects in memory at one point, and then all the objects in memory at another point.  In my case, any overlap would signify a memory leak.  Is there someway I could do that?

----

The command line tool 'heap' does what you're asking, but it sounds like you should give ObjectAlloc a try first.  You can set it up to record every allocation/retain/release/free of any object and look at the history of both current and freed objects.  When I'm having a problem, that's usually enough that I can figure out where to look. My method is pretty much:

*Launch it and find my classes.
*Do something that I think should make one or more of them get freed.
*If it didn't, use the instance browser and look at the retain/release history to see if it's an extra retain or a missing release.  Since it gives stack traces of each alloc/retain/release, you can just step through them to see if it's being balanced properly.
*If it does, then repeat with the 'downstream' classes.

 If you used that, you should be able to figure out what classes aren't being freed, which will  point you in the right direction at least.  And being able to see the retain/release history will help you see what object is retaining but not releasing. -- Bo

----

What are General Blocks (and why am I allocating so many of them? And what do the number next to them mean? -32, 16, 144)

----

General blocks are blocks that do not refer to either ObjC or CoreFoundation objects, i.e. that are malloc'ed directly.  The number after the dash is the size of the block in bytes, and they are grouped by size.    -- Bo

----

How accurate is leaks? I'm on 10.4.7 on Intel, and I'm seeing a lot of ostensible leaks that don't look very plausible to me. The output seems to be very variable as well.

Do I need to worry about everything it reports, or are there some false positives in there?

----
In theory, both false positives and false negatives are possible. In practice, false positives are very rare unless you're running evil code (and Apple's code doesn't seem to be evil).

Have you turned on MallocStackLogging? Maybe these really are leaks. Post a sample if you're not sure.

