---
layout: page
title: CavemanDebugging
---

CavemanDebugging is my term for sprinkling printfs or NSLogs into code to get an ad-hoc code trace and peering in on the state of program variables.  Crude, but effective at times.  ++MarkDalrymple

----

It's particularly effective in some of the following situations:


* a long-running operation that encounters a problem early on, but doesn't fail/crash until much later - intelligent logging can help you understand how your program's state changes as time progresses
* a real-time operation (like burning a CD) where you can't really slow things down with the debugger
* race conditions between threads


----

I have (amongst other) these macros (defined to nil when NDEBUG is present):
    
#define bug(format, args...) do {\ 
   PrintF("[%s] %-20.20s %-20.20s: ",PrettyTime(),PrettyFunction(__FUNCTION__),PrettyFile(__FILE__));\ 
   PrintF(format , ## args); } while(false)
#define D(name, code) \ 
   if(name.isEnabled()) { code }							
#define DEBUG_VAR(name) \ 
   /* left out the code */


In each file I start with:
    
DEBUG_VAR(Encoding);


And in the same file I can do something like this:
    
D(DBF_Encoding, bug("%d\n", someValue);)


By default none of these statements output anything, but I have another source file which add a Debug-menu to the menubar, where all the variables are present (like Encoding), and I can then, using the menu, enable debug output from all the statements which use the Encoding-debugflag or whatever (and the output is prefixed with source file, function and time of the output).

I also have an ASSERTION macro which does stack dump. And I seriously never find any use for a debugger, on the contrary, that seems primitive to me :-) --AllanOdgaard

----

I've become quite the master of littering my code with NSLog when trying to track a bug. I should probably sit down for a few hours and really learn the debugger.. -- KentSutherland

----

I use CavemanDebugging and the debugger, and other tools (ktrace, leaks, objectAlloc, etc) when they make sense.  With small apps, throwing in some NSLogs and rebuilding can be faster than cranking up the debugger.  If you have a big app with long compile or link times, and don't already use a nice infrastructure like AllanOdgaard described above, then putting in logging can take a chunk of time, so the debugger can be a win.  When I'm dealing with someone else's code for the first time, I like stepping through in the debugger to get a basic lay of the land and control flow for the particular problem, and also figure out what parts I can ignore for the time being (which is very important, since I have limited brain-space).  I also like the debugger when handling crashers, since I can see the stack trace and then poke around the body seeing what went wrong. And it can be handy being able to attach to a running program and poke around, without having to add any code.  (shameless plug: I have a gdb intro over at MacEdition, and I have a chapter in BookCoreMacOSXandUnix on gdb which has a debugging session tracking down a number of errors in a sample program.) ++MarkDalrymple

----

A couple of years ago when sorting out some hairy state transition bugs in OOFILE we instrumented the code with assert(Invariant("some string')) all over the place. The Invariant method added to all classes was of course a true Invariant check but also gave us a central place to put a log.

AndyDent

