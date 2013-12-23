---
layout: page
title: NSTimerWastesCPURandomly
---

 

I have an NSTimer that is used for a simple animation. The animation speed is user configurable, such that the _scrollInterval can be 0.02, 0.05, 0.08... but really that is kind of unrelated... the point is that it has to run pretty frequently.
Now, I have this code, which just invalidates the old timer if it exists and creates a new one, adding it to the 2 additional run loops I need it to fire in.

The problem is, that every time i call setScrollInterval with either the same value or a different value, it randomly changes the CPU usage... sometimes its at 0% and another call will make it jump to 15% or 20%! I would like it to stay at 0%, I have no idea what's wrong. It is at 20% sometimes on the first call, other times its at 0% on the first call :-/ At 0% the animation runs perfectly fine, I am sure the problem is not in redrawFrame.

The only thing I see that could be wrong here is that I am not calling [_scrollTimer release], but when I do that it causes the app to crash after a few calls! :-(

    
- (void)setScrollInterval:(float)newScrollInterval {
	if(_scrollTimer != nil) {
		[_scrollTimer invalidate];
	}
	_scrollTimer = [NSTimer scheduledTimerWithTimeInterval:newScrollInterval 
                         target:self selector:@selector(redrawFrame) 
                         userInfo:nil repeats:YES];	
	[[NSRunLoop currentRunLoop] addTimer:_scrollTimer 
                         forMode:NSModalPanelRunLoopMode];
	[[NSRunLoop currentRunLoop] addTimer:_scrollTimer 
                         forMode:NSEventTrackingRunLoopMode];
}


----

According to the docs, "The NSRunLoop will remove and release the timer, either just before the invalidate method returns *or at some later point*." With documentation like that, who knows what could happen! The addTimer method results in the run loop retaining the timer but it doesn't look like you can count on the invalidate method to get the run loop to release the timer before you reinstantiate and add it again. I would suggest you do a retain when you set up the new timer and a release after you invalidate it (as well as setting it to nil). You could also try alternating between two different timer instances (like _scrollTimer1 and _scrollTimer2). Not sure if that would help, but it might.

----

The first thing I would do is find out exactly *what* is taking up all of that extra CPU time. Run your program under Shark, make the excess CPU usage happen, and see what it is. It's much easier to fix something if you know what needs to be fixed.

----

I have sampled the same process in Shark when it is wasting CPU and when it is not, below are the screenshots of what Shark gave me... very confusing? :-/ For this test, I have made drawRect (called by redrawFrame via the setNeedsDisplay:YES) simpy do an NSLog and then a "return" and it still wastes CPU! redrawFrame consists of:

    
- (void)redrawFrame  {
	[self setNeedsDisplay:YES];
	if(_scrollFlag == YES) {
		_scrollPosition--;
	}
}	


This is while running properly (without CPU waste):

http://www.photocenter.ca/~zigzag/good.jpg

And this is while running at around 15-20% CPU, doing the exact same thing!:

http://www.photocenter.ca/~zigzag/bad.jpg

I've tried switching between two timers, and tried doing a retain and then a release as suggested, neither nor a combination of the two helped.

Here's an NSLog of what is happening basically (showing that there is no extra timer firing in the poor performance situation):

    
GOOD NSLOG (RUNNING BEAUTIFULLY):

2004-10-01 09:28:45.384 NewsTicker[4993] setScrollInterval:0.050000
...
2004-10-01 09:28:46.294 NewsTicker[4993] redrawFrame (_scrollTimer fired)
2004-10-01 09:28:46.301 NewsTicker[4993] end of redrawFrame
2004-10-01 09:28:46.308 NewsTicker[4993] drawRect (called by setNeedsDisplay:YES)

2004-10-01 09:28:46.335 NewsTicker[4993] redrawFrame (_scrollTimer fired)
2004-10-01 09:28:46.351 NewsTicker[4993] end of redrawFrame
2004-10-01 09:28:46.352 NewsTicker[4993] drawRect (called by setNeedsDisplay:YES)

2004-10-01 09:28:46.385 NewsTicker[4993] redrawFrame (_scrollTimer fired)
2004-10-01 09:28:46.386 NewsTicker[4993] end of redrawFrame
2004-10-01 09:28:46.386 NewsTicker[4993] drawRect (called by setNeedsDisplay:YES)

BAD NSLOG (WASTING CPU):

2004-10-01 09:29:49.428 NewsTicker[4994] setScrollInterval:0.050000
...
2004-10-01 09:30:08.878 NewsTicker[4994] redrawFrame (_scrollTimer fired)
2004-10-01 09:30:08.879 NewsTicker[4994] end of redrawFrame
2004-10-01 09:30:08.880 NewsTicker[4994] drawRect (called by setNeedsDisplay:YES)

2004-10-01 09:30:08.928 NewsTicker[4994] redrawFrame (_scrollTimer fired)
2004-10-01 09:30:08.929 NewsTicker[4994] end of redrawFrame
2004-10-01 09:30:08.930 NewsTicker[4994] drawRect (called by setNeedsDisplay:YES)

2004-10-01 09:30:08.978 NewsTicker[4994] redrawFrame (_scrollTi-er fire$)
2004-10-01 09:30:08.979 NewsTicker[4994] end of redrawFrame
2004-10-01 09:30:08.980 NewsTicker[4994] drawRect (called by setNeedsDisplay:YES)


It seems to be some sort of a timing/synchronization thing?

----

I've put together a little Xcode project that shows the problem... I think it might be a Cocoa bug?

http://zigzag.mediapath.com/newsticker/TimerBugDemo.zip

Just run that and click the Start/Stop button a bunch of times while you have a CPU usage monitor open (ie. "top" in Terminal). It will be at 0% the first few times usually, but eventually you'll see it start using 30% or so CPU!

What's going on here? :-/

----

Update : I've sent a bug rep/rt to Apple.

*Just out of curiosity: What's the bug number?*

----

Hello,

I think almost all of what you're seeing (in the test app at least) comes from the NSProgressIndicator.  If I comment out the animation lines, CPU usage stays at 0% no matter what.  Besides that, I wouldn't worry too much if your only indication of trouble is %CPU usage on a machine that isn't under load.  

(Oh, and use of underscore prefixes for private variables is reserved to Apple - http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html )

-Ken

----

I assume you were using top to work out how much CPU time it was using. It is extremely inaccurate and if it samples at the wrong time, it can look like your app is using 100% CPU when in fact it used just a couple of cycles.

----

you might try putting the NSProgressIndicator into threaded mode. each thread gets a separate run loop, so if the progress indicator is contaminating the CPU load of the thread with the timer, moving it into a separate thread should relieve the contamination.

at the very least, it's good practice to use threads when you can. presumably the progress indicator's thread gets set to a low priority so that your work thread gets more time under heavy load. (preemptive scheduling++ :)

*--boredzo*

----

The problem is not related to the progress indicator at all, even without it it will occur. Yes I am using top, however, "top -s 0"... which is fairly accurate, and the CPU stays at 30% when the problem appears. The bug # is 3823900.

A question to the above poster, out of curiosity, how do you set the thread priority?

----

In case the OP is still around:  You're looking at Shark's "Heavy" view.  Try switching the view to "Tree".

