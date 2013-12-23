---
layout: page
title: DistributedObjectsForInterThreadCommsCrash
---



See also DistributedObjectsForInterThreadCommunication.

I'm experiencing a lot of trouble using DistributedObjects for interthread communications. My distributed object code was based fairly closely on Apple's SimpleThreads example, but in its latest variant, is slightly closer to: 

http://developer.apple.com/documentation/Cocoa/Conceptual/Multithreading/articles/CocoaDOComm.html

In my application, I'm getting a crash in __CFRunLoopFindMode quite regularly, especially when I have two worker threads running.

I can replicate this crash in SimpleThreads, although I need to generate a lot more threads to do so quickly and reliably. My SimpleThreads variant creates 10 blocks of 200 threads at a time. The threads don't actually do anything, and are killed before the next block starts. I typically get the crash in about the 7th or 8th block. Although creating this many threads is obviously excessive, I get the same crash, but less reliably and conveniently, in my app with only a handful of threads.

I have posted my SimpleThreads variant at

http://www.mildmanneredindustries.com/downloads/SimpleThreadsCrashes.dmg

There are a few defines in Controller.h that you can use to change the DO threading behavior slightly. They all crash. There is a ReadMe.txt file with details.

I have read up on using DO for inter thread comms fairly extensively, and I don't seem to be doing anything drastically different from anyone else.

A few more things worth noting: 

I get an identical crash on intel and PPC. The crash is located at CFRunLoop.c line 371, which looks fairly innocuous:

   _CFRuntimeSetInstanceTypeID(&srlm, __kCFRunLoopModeTypeID); // Line 371

_CFRuntimeSetInstanceTypeID is a one liner that reads:

    __CFBitfieldSetValue(((CFRuntimeBase *)cf)->_info, 15, 8, typeID);

Strangely, the crash is shown as SIGTRAP in the debugger, and as a Trace/BPT error in a deployment command line version. A screenshot of the debugger on a PPC box is at 

http://www.mildmanneredindustries.com/graphics/CFRunLoopFindModePPC.png

(this is a slightly different test program, which is a command line tool, and with the debug libraries enabled).

The crash has a tendency to bring other applications down with it. Apple Mail often suffers from this.

There is something suspicious about the regularity with which it crashes the test program at about the same point. Richard Low reported a DO issue that sounds similar (http://www.wentnet.com/misc/nsproxy.html) where the crash always occurred at a certain point in the program (although I'm not certain that this was a crash in CFRunLoopFindMode).

Obviously I have a radar issue (4838357) open on this. Any fix for the crash would be wonderful, but in the absence of that, I guess my question is, what alternatives do I have to DO for interthread communication?

Martin

----


----

For what it's worth, I can't get your "Lots of threads" sample to crash (I've downloaded the DMG and compiled the sample).
I can see the unresponsive UI and processor usage around 80% but no crash.

I can see the threads getting born and dying - I can also attach the process with gdb and see the process stuck in thread context switching. Which makes sense when talking about this many threads.
This is how the process looks:

    
cristi:~ diciu$ ps axM -p 552
USER    PID  TT  %CPU STAT PRI     STIME     UTIME COMMAND
diciu   552  ??    0.0 S     46   0:03.53   0:18.38 /Users/diciu/Desktop/st/build/Default/SimpleThreads.app/Contents/MacOS/SimpleThreads
cristi:~ diciu$ ps axM -p 552
USER    PID  TT  %CPU STAT PRI     STIME     UTIME COMMAND
diciu   552  ??    0.0 S     46   0:03.53   0:18.38 /Users/diciu/Desktop/st/build/Default/SimpleThreads.app/Contents/MacOS/SimpleThreads
cristi:~ diciu$ ps axM -p 552
USER    PID  TT  %CPU STAT PRI     STIME     UTIME COMMAND
diciu   552  ??   53.1 R     24   0:03.62   0:18.71 /Users/diciu/Desktop/st/build/Default/SimpleThreads.app/Contents/MacOS/SimpleThreads
        552        0.0 S     46   0:00.00   0:00.00                 
        552        0.0 S     46   0:00.00   0:00.00                 
        552        0.0 S     46   0:00.00   0:00.00                 
        552        0.0 S     46   0:00.00   0:00.00                 



I'm on a Powerbook 17* with 1.25 Gb of RAM, 10.4.8.

--
CristianDraghici

----


----

Actually, that's worth quite a lot. I have managed to get it to run once (only once) through without crashing. Can you run it repeatedly without it crashing? I got the crash very reliably on PowerPC. Comment out the ORIGINAL_RUN_LOOP define in Controller.h for best (worst) results.

-- Martin


----

you're right.
uncommenting the line makes it much worse. I've previously ran it two times w/o errors. The new version crashes every time.

Using Build and Run under XCode kills XCode as well. I don't get a crashdump either.

Under gdb, I get the backtrace you describe:

    
Program received signal SIGTRAP, Trace/breakpoint trap.
[Switching to process 1664 thread 0x25c603]
0x907da280 in __CFRunLoopFindMode ()
(gdb) bt
#0  0x907da280 in __CFRunLoopFindMode ()
#1  0x907da500 in CFRunLoopAddSource ()
#2  0x92952084 in -[NSMachPort scheduleInRunLoop:forMode:] ()
#3  0x92a3d4b4 in +[NSRunLoop _runLoop:addPort:forMode:] ()
#4  0x92952b54 in -[NSMachPort addConnection:toRunLoop:forMode:] ()
#5  0x92952a84 in -[NSConnection addPortsToRunLoop:] ()
#6  0x92952a2c in -[NSConnection addPortsToAllRunLoops] ()
#7  0x0000903c in +[TransferServer connectWithPorts:] (self=0x10000, _cmd=0x3, portArray=0xc180) at TransferServer.m:128
#8  0x92961194 in forkThreadForFunction ()
#9  0x9002b508 in _pthread_body ()



I can't read much assembler but I'm guessing somehow the process runs out of mach ports and fails to check against mach_port_allocate failure...

    
0x907da25c <__CFRunLoopFindMode+220>:   lwz     r3,0(r2)
0x907da260 <__CFRunLoopFindMode+224>:   bl      0x9086ae40 <dyld_stub_mach_port_allocate>      // it's probably trying to alloc a mach port
0x907da264 <__CFRunLoopFindMode+228>:   li      r5,0
0x907da268 <__CFRunLoopFindMode+232>:   cmpwi   cr7,r3,0
0x907da26c <__CFRunLoopFindMode+236>:   bne-    cr7,0x907da274 <__CFRunLoopFindMode+244>
0x907da270 <__CFRunLoopFindMode+240>:   lwz     r5,112(r1)
0x907da274 <__CFRunLoopFindMode+244>:   cmpwi   cr7,r5,0
0x907da278 <__CFRunLoopFindMode+248>:   stw     r5,36(r30)
0x907da27c <__CFRunLoopFindMode+252>:   bne-    cr7,0x907da284 <__CFRunLoopFindMode+260>   // and fails as it doesn't make this jump here
0x907da280 <__CFRunLoopFindMode+256>:   trap


I've also found this: http://flickr.com/photos/agentdero/153512115/


Please do update this page once Apple Radar qualifies your bug report.

Thanks,
--
CristianDraghici

----

Cristian - that's very interesting - "somehow the process runs out of mach ports and fails to check against mach_port_allocate"

If there is a fixed mach_port limit, that would explain why the problem regularly occurs in a certain region of the test program's execution (although I was looking at a case just now where having crashed once, it crashes very quickly on subsequent runs).

If I put a breakpoint on the instruction prior to the trap, and rerun the test program, "step into" takes me to __CFMachPortAllocate, although it wasn't clear to me how that related to line 371 of __CFRunLoopFindMode, which is where the crash is reported as occurring.

    
static __CFPort __CFPortAllocate(void) {
    __CFPort result;
    kern_return_t ret;
    ret = mach_port_allocate(mach_task_self(), MACH_PORT_RIGHT_RECEIVE, &result);
    if (KERN_SUCCESS == ret) {
        ret = mach_port_insert_right(mach_task_self(), result, result, MACH_MSG_TYPE_MAKE_SEND);
    }
    if (KERN_SUCCESS == ret) {
        mach_port_limits_t limits;
        limits.mpl_qlimit = 1;
        ret = mach_port_set_attributes(mach_task_self(), result, MACH_PORT_LIMITS_INFO, (mach_port_info_t)&limits, MACH_PORT_LIMITS_INFO_COUNT);
    }
    return (KERN_SUCCESS == ret) ? result : CFPORT_NULL;
}


    
/* call with rl locked */
static CFRunLoopModeRef __CFRunLoopFindMode(CFRunLoopRef rl, CFStringRef modeName, Boolean create) {
    CFRunLoopModeRef rlm;
    struct __CFRunLoopMode srlm;
    srlm._base._isa = __CFISAForTypeID(__kCFRunLoopModeTypeID);
    srlm._base._info = 0;
    _CFRuntimeSetInstanceTypeID(&srlm, __kCFRunLoopModeTypeID); // line 371
    srlm._name = modeName;
    rlm = (CFRunLoopModeRef)CFSetGetValue(rl->_modes, &srlm);


-- Martin

----

This is all speculation on my part but I imagine CFRunLoop is _not_ a loop per se.
But rather it's the equivalent of a UNIX poll call waiting on a set of file descriptors to be marked active. And in our case I imagine it's the mach ports that get activity and awake the process from waiting on the run loop.
i see run loop like this:
- wait on activity on given set of mach ports
- if activity awake from the loop and execute something
- expose a method for 3rd parties to add to our set of watched mach ports

A bit of background on poll - sorry if this is not the case - In the UNIX world say I'm a server and I have 20000 clients.
I would keep 20000 connections open with all these clients but how would I know which one to process (i.e. which client is talking to me right now) - iterating through 20000 FDs and trying to _read_ from them, even if trying to _read_ in non blocking mode would be very inneficient.

So we have the poll UNIX call where set up a set of file descriptors to watch (our 20000 in this case).
We then block in the _poll_ call and are awaken by the OS which has previously marked interesting FDs in our FD set.

Now I'm not sure what FindMode does so that it needs allocating ports but unless we completely understand how RunLoops work in OS X.

PS I don't have Amit Singh's book on Mac OS X but I did get some very competent answers from him a while back so if you have access to it it might be worth checking out.

--CristianDraghici

----

I think line 371 is suspect.
It should crash here (i'm reading the source from opendarwin):

    
/* call with rl locked */
static CFRunLoopModeRef __CFRunLoopFindMode(CFRunLoopRef rl, CFStringRef modeName, Boolean create) {
    CFRunLoopModeRef rlm;
    struct __CFRunLoopMode srlm;
    srlm._base._isa = __CFISAForTypeID(__kCFRunLoopModeTypeID);
    srlm._base._info = 0;
    _CFRuntimeSetInstanceTypeID(&srlm, __kCFRunLoopModeTypeID);
    srlm._name = modeName;
    rlm = (CFRunLoopModeRef)CFSetGetValue(rl->_modes, &srlm);
    if (NULL != rlm) {
	__CFRunLoopModeLock(rlm);
	return rlm;
    }
    if (!create) {
	return NULL;
    }
    rlm = (CFRunLoopModeRef)_CFRuntimeCreateInstance(CFGetAllocator(rl), __kCFRunLoopModeTypeID, sizeof(struct __CFRunLoopMode) - sizeof(CFRuntimeBase), NULL);
    if (NULL == rlm) {
	return NULL;
    }
    rlm->_lock = 0;
    rlm->_name = CFStringCreateCopy(CFGetAllocator(rlm), modeName);
    rlm->_stopped = false;
    rlm->_sources = NULL;
    rlm->_observers = NULL;
    rlm->_timers = NULL;
    rlm->_submodes = NULL;
    rlm->_portSet = __CFPortSetAllocate();
    if (CFPORT_NULL == rlm->_portSet) HALT;   // i think this is the trap we see in gdb, right?


considering:

    
CF_INLINE __CFPortSet __CFPortSetAllocate(void) {
    __CFPortSet result;
    kern_return_t ret = mach_port_allocate(mach_task_self(), MACH_PORT_RIGHT_PORT_SET, &result);
    return (KERN_SUCCESS == ret) ? result : CFPORT_NULL;
}


I think you're running out of mach ports.

-- CristianDraghici

----

Bingo! Kirk Kerekes actually gave me the vital clues, but Cristian is spot on.

All of the Apple sample code, and other published example code that I've been able to find, leaks mach_ports pretty badly. Enabling the mach_ports column in Activity Monitor allows you to observe this leakage relatively directly. With ORIGINAL_RUN_LOOP commented out in the code I posted, I get roughly seven leaked mach_ports per thread. When the process reached about 11,000 mach_ports, it crashed in __CFRunLoopFindMode, almost certainly where Cristian indicates above.

I found a fix that leaked only one mach_port per thread. Interestingly, the absolute number of mach_ports does not appear to be the critical factor. I couldn't get this new version to crash, even after generating about 50,000 threads, and a corresponding number of leaked mach_ports. One final change removed the mach_port leakage altogether, and also does not crash.

The fix is as follows. The method that you call from the "parent" thread to kill the child should look like this:

    
- (void)killThread: (id) server
{
    [server stopServer:self];
    
    [server disconnect];
    NSConnection *connection = [(id) server connectionForProxy];
    connection receivePort] invalidate];
    [[connection sendPort] invalidate];
    [connection release];
};


Where stopServer: is a method that sets the boolean flag indicating that the child thread should stop running (see http://developer.apple.com/documentation/Cocoa/Conceptual/Multithreading/articles/[[CocoaDOComm.html or the sample code I posted).

The disconnect method is the one suggested by John Nairn (http://www.cocoabuilder.com/archive/message/cocoa/2002/8/23/51214) to fix the SimpleThreads code:

    
- (oneway void)disconnect
{
    NSArray *connectionsArray=[NSConnection allConnections];
    NSEnumerator *connectionsArrayEnumerator=[connectionsArray 
objectEnumerator];
    NSConnection *thisConnection;

    while(thisConnection=[connectionsArrayEnumerator nextObject])
    {    if([thisConnection rootObject]==self)
        {  [thisConnection invalidate];
            break;
        }
    }
}


An alternative fix, as quite a few people suggested, is to use some kind of pool of threads, instead of creating a new thread each time. I'm already running a fix based on that, which seems to work (although I recall it did give me some unrelated problems when I tried it before).

I'm pretty surprised both that the sample code (including Apple's) leaks mach_ports so badly, and that the (hopefully) correct solution was so obscure. If you're using DO in this way, you are almost certainly leaking mach_ports, although for a small number of threads, you would probably never know.

-- Martin

