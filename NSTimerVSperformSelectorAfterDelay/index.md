---
layout: page
title: NSTimerVSperformSelectorAfterDelay
---

hi !
can someone basically explain the difference between a NSTimer that will fire and execute a method in a delay and the call to performSelector:afterDelay: ?

which one is the "lightest" ? i don't need extreme precision in delay.
thanks !

----

NSTimer is generally more flexible. You can more easily cancel the timer, you can make the timer repeat, you can query the timer, and you can make the timer invoke an arbitrary method call using NSInvocation. NSTimer is probably slightly more expensive, since it's a separate object, but I doubt if this is noticeable in most cases. Both methods rely on the runloop for scheduling and will have the same accuracy.

----

You really need to do some testing as to which one is faster. If it were me writing this, I'd implement performSelector:afterDelay: with an NSTimer.

*Why do you really need to test it? Unless you're calling it thousands of times per second, the speed difference will not be important.*

**I just checked with some breakpoints;  calling performSelector:afterDelay: does not create a new NSTimer.**

*The documentation says it uses a "timer, " so it sounds like it's either creating an NSTimer or using the underlying CoreFoundation calls without explicitly creating an NSTimer.*

**Yep yep, should have thought of that before.  Tests with breaking on CFRunLoopTimerCreate are interesting but not exactly conclusive.  *Everything* causes that function to be called.  performSelector:withObject:afterDelay: causes it to be called one time, but so does performSelector:withObject:.  In a normal running app it's called a heck of a lot.**

----

AFAIK performSelector:afterDelay is cancelable too, and yes i will need to cancel it because currently i'm always testing if the timer exists (it is in a loop ), and create another timer at each cycle
    

// this code is in a loop
if(timer) {
[timer invalidate];
[timer release]
timer = nil;
}

timer = [[NSTimer scheduledTimerWith...] retain];


is it possible to change the fireDate of the timer on the fly ? this would be a clear advantage over performSelector:afterDelay: that need a cancel first

----

Timer cancelling is a bit more flexible. It's not possible to have two delayed performs pending with the same target, selector, and argument and only cancel one of them.

As far as changing the fire date, yes, rtfm.

duuuuude.... "rtfm" should not be in our vocab.  The 'm' in question is HUGE, and half the purpose of this site is to answers questions that have answers but are hard to find.  In this case, a simple check on file:///Developer/ADC%20Reference%20Library/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSTimer.html#//apple_ref/occ/cl/NSTimer would have answered the question, but still....  sheeeesh!

----

NSTimer has a whole *eight* instance methods, one of which is very obviously the answer to the question. If you can't read through a half-screen header file to check for the answer to an extremely simple question, how do you expect to be able to program a computer at all? This question was *not* hard to find. If you think it was, you're not trying hard enough.

----

hmmm well, the problem is not to READ the manual here, because 1/ it must be well written which is not the case with Apple, even if it is better than in the 10.1 days and 2/ that you understand my question (or that i clearly explain mine) : when i ask "is it possible to change the fireDate of the timer on the fly ?" i already knew the setFireDate: method (i have 3 years of cocoa professional development)  but what is not clearly answered in the manual here is "on the fly". Can i create a timer, then *before* it fires, modify its fire date to make it fire later... that is *my* notion of "on the fly" but maybe i was not clear enough... that would avoid me to create a new timer each time.
simpler ?

*I think you're taking it too hard. You're treating "rtfm" as if it were "read the f*cking manual, you idiot!" My intention was simply to say "it's better to resort to the manual than to this web site." If you took it badly, I'm sorry.*

----

Well, I suppose the thing to do would be to try it.  Should only take a minute... Yes, you can change the fire date before or after the timer has first fired.  The time interval is not affected.  

By the way, is this Erik in regard to < http://nslog.com/archives/2004/11/25/nstimers_need_voidpauseidsender.php >?  If so I'll note that, (a) I misunderstood the intent of the blog posting, (b) this is an effective way to pause and resume a timer.

-KenFerry

---- 
<CORNY_MUSIC>As the NSTimer turns ... so are the days of our lives ... </CORNY_MUSIC>

