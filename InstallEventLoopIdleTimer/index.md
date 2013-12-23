---
layout: page
title: InstallEventLoopIdleTimer
---

CocoaDiscussions - Sample code for an idle timer? 
Sample code for an idle timer? Please?

Here's what I've ot right now:
    

#include <Carbon/Carbon.h>

void DisplayAlert(CFStringRef Message)
{
	DialogItemIndex LMyDialogItemIndex;
	DialogItemIndex *LMyDialogItemIndexPointer;
	DialogRef LAlertDialogRef;
	DialogRef *LAlertDialogRefPointer;
	LMyDialogItemIndexPointer = &LMyDialogItemIndex;
	LAlertDialogRefPointer = &LAlertDialogRef;
	
	OSStatus		er;
		
er = CreateStandardAlert (1,Message,CFSTR("great!"),nil,LAlertDialogRefPointer);
er = RunStandardAlert (LAlertDialogRef,nil,LMyDialogItemIndexPointer);
}



void DoIdle(EventLoopTimerRef inTimer,  void * inUserData, EventLoopIdleTimerMessage inState)
{
DisplayAlert(CFSTR("Idling"));
}


int main(int argc, char* argv[])
{
    IBNibRef 		nibRef;
    WindowRef 		window;
    
    OSStatus		err;
	
	double PreDelay=0;
	double Interval=1;
	EventLoopTimerRef	MyIdleTimer;
	EventLoopIdleTimerProcPtr DoIdlePointer;
	EventLoopTimerUPP DoIdleUPP;
	EventLoopTimerRef *MyIdleTimerPointer;
	//
	DialogItemIndex MyDialogItemIndex;
	DialogItemIndex *MyDialogItemIndexPointer;
	//
	DialogRef AlertDialogRef;
	DialogRef *AlertDialogRefPointer;
	//
	//
	MyIdleTimerPointer = &MyIdleTimer;
	MyDialogItemIndexPointer = &MyDialogItemIndex;
	AlertDialogRefPointer = &AlertDialogRef;
	
	Interval = Interval*kEventDurationSecond;
	PreDelay = PreDelay*kEventDurationSecond;
	

    err = CreateNibReference(CFSTR("main"), &nibRef);
    require_noerr( err, CantGetNibRef );
    err = SetMenuBarFromNib(nibRef, CFSTR("MenuBar"));
    require_noerr( err, CantSetMenuBar );
    DisposeNibReference(nibRef);


DisplayAlert(CFSTR("Hey"));

err = InstallEventLoopIdleTimer (
   GetMainEventLoop (),
   PreDelay,
   Interval,
   NewEventLoopIdleTimerUPP( &DoIdle ),
   0,
   MyIdleTimerPointer
);

// Call the event loop
RunApplicationEventLoop();

CantCreateWindow:
CantSetMenuBar:
CantGetNibRef:
	return err;
}



But XCode doesn't seem to like the pointer to my Do Idle routine.

I'm realatively new to calling Tool box stuff and to C so I'm sure I'm doing something dumb, but a working example would be great.

The aim here is (of course) to detect when the user has done nothing for a certain amount of time (as in the trigger for a screen saver).

Thanks!

----

You want to use NewEventLoopTimerUPP. There is no NewEventLoopIdleTimerUPP function.

----

Well, I thought so too...BUT:

passing    <code> NewEventLoopTimerUPP( &DoIdle ), </code>  I  get: passing arg 4 of  'InstallEventLoopIdleTimer' from incompatible ponter type

AND Also: passing arg 1 of 'NewEventLoopTimerUPP' from incompatible pointer type  (a result of the &DoIdle somehow)

WHILE passing <code> NewEventLoopIdleTimerUPP( &DoIdle ), </code> I ONLY get: passing arg 1 of 'NewEventLoopIdleTimerUPP' from incompatible pointer type

In other words the compiler (XCode) seems to recognize it and give no warning (however it still doesn't like my pointer to the function - which I suspect is somehow the main issue here?)

I admit that too many pointers and pointers to pointers have my head swimming I can barely remember enough Pascal to deal, let alone C!

That's why it would be great to see a chunk of code that actually works! So I can see how I've screwed up.

Anyone have anything? Or know where I can find some?

----

Strange, my documentation conflicts with the header files. That's why I recommended that you use NewEventLoopTimerUPP(), but the header actually does have (and want) NewEventLoopIdleTimerUPP().

Looking at the header, your declaration for DoIdle() doesn't match the prototype. You need to reverse the second and third parameters.

----

Thanks! OK! Now I'm not getting any warnings! Progress! BUT:

Wouldn't one expect to see the "Idling" alert every second (provided the user isn't moving the mouse or typing)? - Well it's not happening!

I'm not sure how to even begin to figure out why.

One thing I HAVE noticed, it now doesn't care if I have the & in front of DoIdle or just the name DoIdle. It doesn't warn or crash either way.

(And I've actually seen documentation with both versions [I think] which is kind of bizarre)

So how do I figure out what this thing is actually DOING? Is the Event Loop Timer ever firing? Is DoIdle getting called? 

UPDATE:

I tried substituting in a normal (not idle) loop timer in place of the IdleTimer:

    
err = InstallEventLoopTimer (
   GetMainEventLoop (),
   PreDelay,
   Interval,
   NewEventLoopTimerUPP( &DoIdol ),
   0,
   MyIdleTimerPointer
);


(DoIdol is the same routine as DoIdle above without the InState parameter)

And it works! (it Alerts every second) Now WHY won't the EventLoopIdleTimer version work!?!?

----
PROBLEM SOLVED.
For anyone who cares, it looks like you have to pass a non-zero value to inFireDelay (2nd parameter of  InstallEventLoopIdleTimer) to successfully start the whole process (although the documentation claims otherwise).

It's working pretty well now.

