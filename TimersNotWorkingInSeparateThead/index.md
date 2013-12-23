---
layout: page
title: TimersNotWorkingInSeparateThead
---

Howdy y'all,

I have a separate thread in which I want to send a delayed message. I've tried using     performSelector:withObject:afterDelay: and     scheduledTimerWithTimeInterval:invocation:repeats:, but neither of them work, even though they DO work in the main thread. That leads me to believe no timers at all are working in the separate thread. Are there any catches or anything I have to get around to use timers in a separate thread?

Thanks.

----
You have to run a runloop, otherwise they won't fire. If that's not it, General/PostYourCode.

----
Hmm, that's not doing anything... I'm using performSelector:withObject:afterDelay:, so I shouldn't have to add a timer to the run loop, should I? Anyway, here's my code:

    
- (void)awakeFromNib
{
	General/[NSThread detachNewThreadSelector:@selector(test) toTarget:self withObject:nil];
}

- (void)test
{
	General/NSAutoreleasePool *pool = General/[[NSAutoreleasePool alloc] init];
	General/[[NSRunLoop currentRunLoop] run];
	
        // This line works fine...
	General/NSLog(@"will now play iTunes in 2 seconds...");
	
        // ...but this line doesn't do anything.
	[eyeTunes performSelector:@selector(play) withObject:nil afterDelay:2];
	
	[pool release];
}


----
Put your runloop at the end, not at the begnining. Your current order of code makes no sense.

Normally     -run will not return, and your remaining code will not run. However, runloops exit when they have nothing to do, and this one definitely has nothing to do, so     -run returns instantly. You then execute your log, and your delayed perform. Then you exit your thread, so the delayed perform never runs.

By putting your runloop at the end of the method, you'll ensure that it has something to do, and it should work as expected.

Note that you should never *rely* on     -run exiting due to not having anything to do; the system is allowed, and often does, add its own stuff to runloops so this technique is extremely unreliable. It is something to be wary of, not something to take advantage of.

----

Thanks, that made it work. But what if I have multiple timers that I need to fire in the same method of the separate thread? For example...

    
- (void)test
{
	[eyeTunes performSelector:@selector(play) withObject:nil afterDelay:2];
	General/[[NSRunLoop currentRunLoop] run];

        // some other code here...
	
	[eyeTunes performSelector:@selector(pause) withObject:nil afterDelay:2];
	General/[[NSRunLoop currentRunLoop] run];
}


Going by what you said, it's a bad idea to have that first     General/[[NSRunLoop currentRunLoop] run]; call in there, because it might not return. But if I only call it at the end of my method, the first delayed perform won't get executed before the "// some other code here...". (And I do need the first delayed perform to get executed before the other stuff.) How can I make both delayed messages get executed on time?

----
If you need a strong ordering between the two then you should be calling the pause thing (either directly or delayed) from your run method.

----
Sorry, I'm not quite sure what you mean by "from your run method". Which run method?

Would it work if I call     General/[[NSRunLoop currentRunLoop] run]; at the beginning, and then use     scheduledTimerWithTimeInterval:invocation:repeats: for the delayed performs, since that method automatically adds itself to the run loop?
