---
layout: page
title: PerformSelectorAfterDelay
---


I'm trying to use performSelector:withObject:afterDelay: in my .mailbundle, but it hasn't been working correctly.  Using performSelector:withObject:  works just fine, but once I add a delay, such as     [self performSelector:@selector(mySelector) withObject:nil afterDelay:0.5];, the selector does not get called.  I thought that maybe it had to do with the modes that are available through performSelector:withObject:afterDelay:inModes: and the fact that I'm running this in a .mailbundle, but after limited testing, that didn't work either.  Does anyone have any suggestions into getting this to work?  Thanks!

----

Well, what is the thread doing after issuing that command?  As long as you have a runloop which you are going to return to, this should work.

--JeffDisher

----

Is the any NSRunLoop<nowiki/>s running in your thread? Are you trying to run     performSelector::: from a secondary thread?
If you want to delay selector to the next run loop sequence, try to use this:

    
id currentRunLoop = [NSRunLoop currentRunLoop];
id defaultRunLoopModeArray = [NSArray arrayWithObject:NSDefaultRunLoopMode];	
[currentRunLoop performSelector:@selector(mySelector)
	target:self
	argument:nil
	order:0
	modes:defaultRunLoopModeArray];


If you just want a delayed perform, NSTimer is a safer way to do it:

    
[NSTimer scheduledTimerWithTimeInterval:0.5
	target:self
	selector:@selector(myTimerHandler:)
	userInfo:nil
	repeats:NO];

/* */

- (void) myTimerHandler:(NSTime *)aTimer {
   [self mySelector];
}


As you can see, the only difference here is a dedicated method which is needed to handle timer firings.

-- DenisGryzlov

----
What, exactly, is supposed to be the difference between using     performSelector:withObject:afterDelay: and the above code snippets? They all accomplish the exact same thing and have the same limitations. -- MikeAsh

