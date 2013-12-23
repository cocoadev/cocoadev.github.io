---
layout: page
title: NSTaskTermination
---



Editor's note: This material has been moved here from the main NSTask page, to discuss specific NSTask termination gotchas http://goo.gl/OeSCu

1) NSTask provides methods for launching an application and terminating the launched process; these are the NSTask methods     launch and     terminate respectively.

Launching and terminating take time to affect the launch and termination although both methods return immediately when called.
Give your launched application time to launch before using it. You may have to use locks/notifications or distributed objects to do this. The termination is easier because NSTask will send a notification to your application called NSTaskDidTerminateNotification. Note the comments in Apple's documentation about receiving NSTaskDidTerminateNotification. If your NSTask is destroyed before the notification is sent you won't receive the notification.

2) At the time of writing, April 2003 with Build: Mac OS X 10.2.5 (6L29), NSTask will post your NSTaskDidTerminateNotification to the thread in which your task was launched. If you have a multi-threaded application this can be significant.

Here's the scenario; you launch an application, using NSTask, from a thread you detached (let's call it thread 2) from your App's primary thread (thread 1). NSTask will store information about the launching thread, thread 2. When your launched application terminates NSTask will post its NSTaskDidTerminateNotification to your launching thread, thread 2. Everything will work out if thread 2 still exists. If thread 2 has exited you may see your application crash with a stack trace like this:

    
<crash log>
Exception:  EXC_BREAKPOINT (0x0006)
Code[0]:    0x00000001Code[1]:    0x9014819c

Thread 0:
 #0   0x90073c48 in mach_msg_trap
 #1   0x90005f90 in mach_msg
 #2   0x901489f0 in __CFRunLoopRun
 #3   0x90180f58 in CFRunLoopRunSpecific
 #4   0x969a3b70 in RunCurrentEventLoopInMode
 #5   0x969b3b00 in ReceiveNextEventCommon
 #6   0x969dabbc in BlockUntilNextEventMatchingListInMode
 #7   0x9308dedc in _DPSNextEvent
 #8   0x930a0158 in -[NSApplication nextEventMatchingMask:untilDate:inMode:dequeue:]
 #9   0x930b1d88 in -[NSApplication run]
 #10  0x9315fc58 in NSApplicationMain
 #11  0x00003ab4 in main (main.m:18)
 #12  0x000037fc in _start (crt.c:267)
 #13  0x0000367c in start

Thread 1:
 #0   0x9003eaa8 in semaphore_wait_signal_trap
 #1   0x9003e8c4 in _pthread_cond_wait
 #2   0x90824180 in -[NSConditionLock lockWhenCondition:]
 #3   0x930819a0 in -[NSUIHeartBeat _heartBeatThread:]
 #4   0x9081cd38 in forkThreadForFunction
 #5   0x90020d48 in _pthread_body

Thread 2 Crashed:
 #0   0x9014819c in CFRunLoopWakeUp
 #1   0x9083183c in _waitForTermination
 #2   0x90020d48 in _pthread_body
</crashlog>


This is a problem I had with my application. I'd launched another process from my app and destroyed the launching thread. Everything worked until my launched-app terminated; then my launching app crashed. The confusing part was Thread 2 in the trace; I knew I only had 2 threads running when my app crashed (threads 0 and 1) - where did thread 2 come from? The answer is thread 2 was the launching thread and now NSTask was trying to post its NSTaskDidTerminateNotification to a non-existent thread.

Launching from my app's primary (thread 0) thread solved the problem.

----

I have a shell script that I want to execute programmatically. Is NSTask the best method to do it (passing in the arguments) or not? I have not managed to get that method working properly.

It depends.  If you want to call it and just get a result status, the system() function will do that.  If you want to feed your program data (or read data - but not both even though the man page says you can), popen() can work.  These are a lot less cumbersome than using NSTask.  If you're wanting to feed it data and read data from the shell script, or want more control over the execution environment, then NSTask is groovy. ++MarkDalrymple

----

    system and     popen have a couple of serious disadvantages. One, they are both rather high-overhead, as they execute a shell, the shell parses the command, and then executes the command.

The more significant disadvantage is that they will have difficulties with non-UNIX-style pathnames. If your shell script is embedded in your application or you want to send it files that aren't in a standard location, you have to be very careful with pathnames to ensure they're properly escaped. NSTask has none of these problems, as it doesn't invoke a shell (unless you ask it to, in which case the same problems are present).

----

If the NSTask is long running, say like a tail -f, then you MUST terminate it when you application terminates, otherwise the task will continue to run.
Sadly, application termination is abrupt (many dealloc's are not called, etc) and often there is no easy way to invoke NSTask terminate. Also your application may crash, be force quit, or you may be still be developing/debugging it...

In BSD land you can ensure that a child process will terminate by making all processes belong to the same process group - we can leverage this as follows:
    
NSTask *task = ... // setup the task
[task launch];

// create a new group session with us as the leader, or failing that get our current group session
pid_t group = setsid();
if(group == -1) group = getpgrp();

// place into same group, this ensure that when the parent terminates then all terminate
if(setpgid([task processIdentifier], group) == -1) {
    NSLog(@"unable to put task into same group as self");
    [task terminate];
} else {
    ... // set up watching NSThread, listen on standardOutput, etc    
}


Word of caution, this does NOT work when running from within xcode - I assume because xcode has already defined a process group... RbrtPntn

----

Thank you so much RbrtPntn, however I found that if I called setsid() after launching my NSTask, setpgid would fail.  This is what I have:
    
#include <unistd.h>
// . . . code and stuff
NSTask * task = ... // setup task
// create a new group session with us as the leader, or failing that get our current group session
pid_t group = setsid();
if (group == -1) {
	group = getpgrp();
}
[task launch];
// place into same group, this ensures that when parent terminates the task will be terminated as well
if (setpgid([task processIdentifier], group) == -1) {
	NSLog(@"unable to put task into same group as self");
	[task terminate];
	[taskOutputFilehandle closeFile];
	[task waitUntilExit];
	// handle cleanup as result of error
} else {
	// handle running task
}



AlexP

----

Since my application does not use setsid at all, this is what I used, which will always work... Google mach_override for a ppc version of the overriding code. Since I do not need the original function, it simply overwrite the start of the function with a jump. The overwriting does not need to be atomic since we're still single threaded.

    
#include <mach/mach.h>

//--------------------------------------------------------------------------------------
// NSTask calls setsid after fork to isolate the subprocess,
// override the function with a nop
static pid_t setsid_nop(void)
{
	// return the expected value without doing anything
	return getpid();
}
#if defined (__i386__)
mach_error_t mach_override_ptr(void *originalFunctionAddress, const void *overrideFunctionAddress)
{
	long *originalFunctionPtr = (long*)originalFunctionAddress;
	mach_error_t err = vm_protect(	mach_task_self(),
									(vm_address_t) originalFunctionPtr,
									sizeof(long), false, (VM_PROT_ALL | VM_PROT_COPY) );
	if (err) return err;

	int32_t addressOffset = ((int32_t)overrideFunctionAddress - (int32_t)originalFunctionPtr - 5);
	addressOffset = OSSwapInt32(addressOffset);
	
	uint64_t jumpRelativeInstruction = 0;	
	jumpRelativeInstruction |= 0xE900000000000000LL; 
	jumpRelativeInstruction |= ((uint64_t)addressOffset & 0xffffffff) << 24;
	jumpRelativeInstruction = OSSwapInt64(jumpRelativeInstruction);		
	
	*(uint64_t *)originalFunctionPtr = jumpRelativeInstruction;
	return err;
}
#endif

//--------------------------------------------------------------------------------------
int main(int argc, char *argv[])
{
	mach_error_t err = mach_override_ptr((void*)setsid, (void*)setsid_nop);
	if (err) NSLog(@"mach_override setsid failed: %i", err);

    return NSApplicationMain(argc,  (const char **) argv);
}


Alex2

----

see also discussion at NSTaskWaitUntilExit

