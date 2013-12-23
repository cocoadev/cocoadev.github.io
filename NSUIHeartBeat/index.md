---
layout: page
title: NSUIHeartBeat
---

Has anyone got any idea exactly what NSUIHeartBeat is? I see it all the time in crash reports, but I cant find anything on it anywhere.

I bet it's there for pulsating the buttons and progress indicators.

----

Yes, that is exactly what it is. 

----

Do we have an Apple employee?

----

No comment.

----

That means yes

----

I'm pretty sure its just for the interface buttons to pulse in time... what does gnustep use?

----

I haven't used gnustep in awhile, but I'm pretty sure it doesn't pulse its buttons.

----

Doesn't gnustep have a very NeXT/OPENSTEP interface? The pulsing is Aqua only.

----

Yah, therefore if something exists like that in Gnustep, it can't just be for the pulsing buttons. ;)

----

It may also be used for general UI timing issues - sheet animations, zoom effects, 'the genie effect'... I suppose some of that could apply to gnustep, but I doubt it's there.

If someone is seeing it in gnustep (which the original poster did not say he was using), then look at the source and see what it's doing.

----

The original poster is seeing it in Mac OS X only, as he has never run gnustep.

----

Out of the horses mouth "It's a separate thread that handles the pulsing of buttons, the movement of progress bars and timers. It does this by messaging the main thread". 

----

In the case of pulsating buttons at least, it also seems to call the superview's drawRect: and isOpaque methods from the heartbeat thread itself (not by messaging).

----

I would guess that NSUIHeartBeat doesn't drive UI animation (e.g. pulsating buttons, progress bar animation) but is instead used to implement the spinning beach ball and "application not responding" functionality.

I was checking out the system trace functionality of Shark (after reading the recent ADC article at http://developer.apple.com/tools/performance/optimizingwithsystemtrace.html). It looks as though NSUIHeartBeat is a separate thread spawned in addition to the main thread (which would handle the UI run loop) and fires at regular intervals.

NSUIHeartBeat probably polls the main thread at each of these intervals and if it doesn't get a response (e.g. after 5 seconds of no response) will then change the cursor to a beach ball, etc.

Plus the name is a bit of a giveaway...

*That's a nice theory, but the fact is that NSUIHeartBeat is pretty conclusively linked to pulsating buttons, and it's fairly clearly not exhibiting the behavior you described, as can be seen from the fact that a program which is stopped in the debugger (hence no threads running at all) will still display the spinning pizza.*

*Also, the 'spinning pizza', as it seems to be called now :) is displayed automatically by the window server when an application doesn't respond for more than two seconds.*

Surely it's ok to say this now as Tiger is long since released (if not then someone delete it). But NSUIHeartBeat ties into pulsing buttons even more considering that very early Tiger DP's had buttons that acted kinda like a real heartbeat. They would pulse twice real quick then sit for maybe a half a second or so then pulse twice again.

----

It likely was just some Apple employee having fun with the brand new NSAnimation class... nothing else ;)

----

Here is some info about my re-search on NSUIHeartBeat with ClassDump:

    
@interface NSUIHeartBeat : NSObject {
    //returns singleton object for NSUIHeartBeat. It will probably initialize the drawing thread if it's not exists yet.
    + (id)sharedHeartBeat; 

   //some stuff for modifying polling intervals.
   + (double)heartBeatCycle; //On my Mac (iBook G4, 10.4.6) it always returns 0.0, but polling occurs with 0.03s interval
   + (void)setHeartBeatCycle:(double)newCycle;

   ////Following methods should be sent to a shared instance of NSUIHeartBeat
   //Use these two methods to start and stop polling your view from the NSUIHeartBeat thread.
   - (void)addHeartBeatView:(NSView *)myView;
   - (void)removeHeartBeatView:(NSView *)myView;

   //I guess that all drawing shouldn't be done with local lockFocus/unlockFocus. Instead, you must use these:
   - (BOOL)lockFocusForView:(NSView *)myView inRect:(NSRect)drawRect needsTranslation:(BOOL)flag; //flag is an indicator for a flipped views?
   - (void)unlockFocusInRect:(NSRect)drawRect;
}


Next, you need to implement some methods in your view. Actually, only one:

<code>- (void)heartBeat:(void *)topSecret;</code>

The parameter is pointer to a private structure which, I guess, is not useful for most cases.
There is also a -(BOOL)_wantsHeartBeat method that NSUIHeartBeat calls it's client views, but return values doesn't matter in my tests.

What is the main purpose of NSUIHeartBeat use? I guess it may help creating custom progress indicators, that draws their status even if main thread blocks. Why create the separate thread for such task, if there is already one running?

-- DenisGryzlov 

----

A separate thread is needed for usability purposes. Suppose the main thread is stuck or so busy that it cannot process timed events on time. In that case any pulsating button or progress indicator would either become stuck too or exhibit a jerky animation. Moreover, being in another thread, the heartbeat's timer does not interfere with the timers you install on the main thread, which makes animation smoother in a multiprocessor setup. Just an educated guess. -- KonstantinAnoshkin

----

Er, if it isn't obvious, there is *no* good reason to use the heartbeat thread.  It's AppKit private.  Why would you violate encapsulation without a reason?� If you need a thread, spawn a thread.� This leaves Apple free to change their implementation, benefiting everyone.

----

Hello everyone, I just performed a class-dump on iPhoto's Mach-O file, and found out that its burn-button uses NSUIHeartBeat too. 
    
- (void)heartBeat:(CDAnonymousStruct7 *)fp8;

That's the method in BurnButtonCell.h and that struct is this, in CDStructures.h
    
typedef struct {
    double _currentDate;
    double _birthDate;
    double _lastDate;
} CDAnonymousStruct7;

And I found a HeartBeat.h too! 
    
@interface HeartBeat : NSObject
{
    CDAnonymousStruct7 _times;
    NSMutableArray *_clients;
    NSPort *_port;
    NSTimer *_msgSendTimer;
    NSThread *_heartbeatThread;
    NSLock *_clientsLock;
    int _viewCount;
}

+ (id)sharedHeartBeat;
+ (double)heartBeatCycle;
+ (void)setHeartBeatCycle:(double)fp8;
+ (BOOL)isHeartBeatThread;
- (id)init;
- (void)dealloc;
- (void)updateHeartBeatState;
- (void)_activate:(id)fp8;
- (void)addHeartBeatView:(id)fp8;
- (void)removeHeartBeatView:(id)fp8;

