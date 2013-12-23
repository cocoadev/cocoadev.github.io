---
layout: page
title: TheLeaksTool
---

General/TheLeaksTool (/usr/bin/leaks) is a UNIX tool that can help uncover memory leaks in your unix process. Leaks works best when malloc's stack logging is activated via the General/MallocStackLogging shell variable (set the General/MallocHelp environment variable and run some command-line tool).

----

General/TheLeaksTool in Tiger shows a hex dump of whatever it thinks is leaking. Neat!

----
Can someone give a more elaborate description and example for someone who thinks (although brought up with a PDP-11), the terminal is evil ?

----

Here's an example of how it works. First, we create a program that leaks a string:
    
#import <Foundation/Foundation.h>

#include <unistd.h>

int main(int argc, char **argv)
{
    General/NSString *s = General/[[NSString alloc] initWithString:@"I am a happy leak"];
    s = nil; // leaked!
    General/NSLog(@"Looping, pid is %d", getpid());
    while(1)
        sleep(1);
    return 0;
}

Now, we run it with malloc stack logging:
    
Hope:~/shell mikeash$ General/MallocStackLogging=1 ./leak 
(10494) malloc: recording stacks using standard recorder
2005-05-06 11:16:43.103 leak[10494] Looping, pid is 10494

In another shell, we run leaks on the process:
    
Hope:~ mikeash$ leaks 10494
Process 10494: 477 nodes malloced for 81 KB
Process 10494: 2 leaks for 80 total leaked bytes.
Leak: 0x00503d50  size=48
        0x00490020 0x0061006d 0x00200061 0x00200068     .I. .a.m. .a. .h
        0x00610070 0x00700079 0x0020006c 0x00650061     .a.p.p.y. .l.e.a
        0x006b0000 0x00000000 0x00000000 0x00000000     .k..............
        Call stack: [thread 2437]: | 0x0 | start | _start | main | -General/[NSPlaceholderString initWithString:] 
Leak: 0x00503de0  size=32       string '?rvt'
        Call stack: [thread 2437]: | 0x0 | start | _start | main | -General/[NSPlaceholderString initWithString:] | General/CFStringCreateWithCharactersNoCopy | __CFStringCreateImmutableFunnel3 | _CFRuntimeCreateInstance | General/CFAllocatorAllocate 

In the output, we find exactly what leaked, its contents, and where it was allocated. We also found another string which the frameworks leaked; hopefully it's a one-time leak.
----

If you're sure you only have one process with a particular name, you can also just do     leaks "App<nowiki/>Name"
----
Hmmm ... I don't seem to be getting the ASCII output bit in the terminal for strings!?!

*are you using Tiger? did you do an upgrade install? do you have the latest dev tools?*

----

Tiger is much more leakproof than earlier versions, which would always gush out strange objects I had nothing to do with. I'm seeing that magical '0 leaks for 0 total leaked bytes' a lot more often now :)

---- 
Thanks a lot for these tips. With General/MallocStackLogging, I can now find the leaks, too! I fixed lots of leaks yesterday in one of my apps, I didn't think it was possible to find so much coding errors without some kind of intelligence.. :-)
