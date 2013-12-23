---
layout: page
title: ForkSafety
---



The     fork() call looks innocent enough when you first see it. It simply copies your process, breaking it into two pieces which appear to be identical, except that one is now the parent and the other is now the child. In terms of code flow,     fork() is simply a function which is called once and returns twice. The OS support required to accomplish this is far from simple, but the concept itself is easy enough.

A basic use of     fork() looks something like this:

    
pid_t pid = fork();
if(pid == 0) { // in child
    ... do child stuff
    exit(0);
}
... in parent


The most common use of     fork() is to execute a subprocess:

    
pid_t pid = fork();
if(pid == 0) {
    execl(path, ...);
}


The     execl() function replaces the current process with a new one, by loading the program at the specified path. In UNIX, the process of starting a new program is divided into two separate pieces, first creating a new process, and second launching a binary. As might be expected, nothing forces you to do them both together. You can simply call     execl() or one of its friends to replace the current process with a new program if you no longer need the current one, and you can simply call     fork() if you want a new process running the same code.

However, due to an odd interaction between multithreading and     fork(), not all is roses. For example, you decide to write a small test in your Cocoa app:

    
- (void)buttonClicked:sender {
    if(fork() == 0) { // in child
        NSLog(@"fork succeeded, we're in the child, and the sender is %@", sender);
    }
}


This code risks the chance of a deadlock in the child. Why?

When     fork() is called, a copy of your process is made. The new process has the same memory contents as the old process at the point that     fork() was called. However, it doesn't have all of the threads. In fact, only the thread which called     fork() exists in the child. The other threads were simply axed, and no longer exist in the child.

This creates a situation which is much like a signal handler, in that those threads could have acquired a lock, and now they will never relinquish it. This makes ForkSafety very much the same as SignalSafety, and you should read SignalSafety if you want to see what can be done on the child side of a     fork() call.

But, you say, my program isn't multithreaded, so I don't have to worry! Well, you're correct that you don't have to worry if your program isn't multithreaded, but how do you know that it's not multithreaded? If your app is a GUI application, then it's multithreaded. Cocoa creates secondary threads for all kinds of things and never tells you about it. The trick is that any other library you link against can do the same. You must be very sure of the libraries you link against before you can assume that your program will never be multithreaded.

If you're calling     fork() just to call     execl() or one of its friends, then the solution is easy; just move all of your questionable setup code to before the call to     fork(), and make sure that your parent branch disposes of any resources it set up. As soon as the     execl() fires, the slate is wiped clean and these concerns go away.

If you have no intention of calling     execl(), then things get much more difficult. In order to be completely safe, you can only call functions which are async-signal safe, and there aren't very many of them. You may be ok if you're just performing a long-running calculation, but you won't be able to use any Objective-C, allocate memory, or call most of libc. You should consider either using a secondary thread rather than a child process, or moving the child process's code into a separate program entirely.

Forking a Cocoa-based application gets tricky. According to     fork(2), "The new process (child process) is an exact copy of the calling process (parent process)" -- some exceptions are listed, but these are not necessarily the only exceptions.  Any open Mach ports are not duplicated; this will cause problems with distributed notifications and distributed objects, to name just a couple of issues.  Immediately calling     execl() after the fork will re-setup the Mach ports.

Note that     daemon() calls     fork() under the hood, so the same caveats apply if you're trying to daemonize with this call.

----

More than tricky.. forking a Cocoa app without exec-ing is not something you can guarantee will work.  It may sometimes look like it works, but you really cannot tell.  See http://lists.apple.com/archives/Cocoa-dev/2006/Dec/msg00097.html for one recent comment from someone who works on Cocoa.

