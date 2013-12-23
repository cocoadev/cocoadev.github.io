---
layout: page
title: NSTimerReleaseProblemWithinNSDocument
---


I'm having problems with an app using the NSDocument structure which constantly checks another object via an NSTimer within the myDocument init method.

    

- (id)init;
{
[super init];

timer = [[NSTimer scheduledTimerWithTimeInterval:0.1 
target:self selector:@selector(checkObject) userInfo:nil repeats:YES]retain];

return self;
}



All is working well until I close the document window and the app crashes.  I'm assuming the retained Timer tries accessing a deallocated object, 
as I can prevent the crash by setting the Timer to repeats:NO.
I've tried creating an NSAutoreleasePool around the timer as suggested in AutoReleasePool, but i'm getting the double free message and the timer doesn't fire.
It's almost as if I need an exit method that would release the Timer when the end user closes the document.

I read you can sometime replace NSTimer with the NSObject method performSelector:withObject:, but I'm not sure this can be constantly repeated.
Am I missing something here. Thanks in advance :)
----
In the dealloc method in your NSDocument, call     invalidate on the timer, which will remove it from the run loop and stop it from firing. You should also release the timer here, since you're retaining it in your init method.
----
Of course, the dealloc is the exit method, thank-you.  
    
- (void) dealloc;
{
[timer invalidate];
[timer release];
[super dealloc];
}

You can imagine my dismay when it still crashed :(

----
Post the backtrace of where it crashed.

----
You don't release timers, or retain them.

----
Of course you do. NSTimer is just an object like any other, and the standard retain/release rules apply.

----
You usually don't have to retain them, since the run loop automatically retains them, and in many cases you don't need to keep a reference to the timer since it's passed to your fire selector. If you do want to keep a reference to it though, than the normal rules stand.

----
Ok, removed the timer retain and all seems fine.

The dealloc method above didn't seem to stop the timer firing, but overriding the -close method with a [timer invalidate] seemed to do the job.  Trouble is I can only open each saved document once!  Nothing appears on a second load attempt.
Does this mean each Instance of myDocuement isn't being deallocated properly? Many thanx for your comments folks.
----

