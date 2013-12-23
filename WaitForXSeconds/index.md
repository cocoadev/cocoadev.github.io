---
layout: page
title: WaitForXSeconds
---

How do I wait for a number of seconds/miliseconds/tenths of seconds etc in Cocoa? I am writing a metronome program...

----

Take a look at General/NSTimer.  You might do something like

    
timer = General/[NSTimer scheduledTimerWithTimeInterval:.5 
          target:ticker
          selector:@selector(tick)
          userInfo:nil 
          repeats:YES];


Now, one problem: General/NSTimer is guaranteed to fire after *at least* the timer interval, but it is allowed to be late (you'll have to see if it looks like this will be a problem).

To get closer to real-time intervals, you probably need to look at General/CoreAudio.  But I'd hold off on that until after you have something working. 

----

But how do I use an General/NSTimer?

20:04

*Well, this is what the documentation is there for. :-) Read it, ask questions if you don't understand.*

Let me rephrase that: How do I actually use that code to have it wait for X seconds? Does the "target"  part indicate a function to go to afterwards?  How do I adeclare/allocate this timer?

*"target" is an object which will be sent the message specified by the "selector" each time the timer fires - kind of like a callback, if you're coming from C.  The method call above wraps in allocation, so there isn't anything else to do.  The timer comes back autoreleased, and also the runloop retains the timer (as the docs mention).  If you don't understand what that means yet then you need to read about cocoa memory management in an organized fashion, since it is pretty impossible to do any cocoa programming without understanding the model.  You could try starting at General/CocoaIntroduction.*

----

Try something like

    

-(void)wait:(double)seconds
{
    General/NSTimeInterval theInterval = 0;
    General/NSDate *date = General/[NSDate date];

    while (theInterval <= seconds)
    {
        theInterval = General/[[NSDate date] timeIntervalSinceDate:date];
    }
}



Your app won't be processing events while you're waiting, but for such small intervals, it might not matter.

*A loop like that is almost never a good idea, and especially not for a metronome.  You are going to take *all* available processor time on one of the machine's General/CPUs, and you're going to take it for as long as the metronome is running.*

----

Just a hack job, but it works ok. The CPU usage is about eight percent on a Dual 1.8 G5. 

    
#import <Cocoa/Cocoa.h>

@interface General/MetronomeView : General/NSView {
    BOOL stopMetronome;
    General/NSDate *startDate;
    float beatsPerMinute;
    float interval, doubleInterval;
    float elapsedTime;
    float nextInterval;
    float pi, maxRadians, minRadians;
    General/IBOutlet General/NSSlider *bpmSlider;
    General/NSMovie *tick;
    General/NSMovieView *movieView;
    int displayCount;
}
- (General/IBAction)sliderAction:(id)sender;
- (General/IBAction)startStop:(id)sender;
@end




    

#import "General/MetronomeView.h"

@implementation General/MetronomeView

- (void)dealloc {
    [startDate release];
    [tick release];
    [movieView release];
    [super dealloc];
}

- (void)metronome {
    elapsedTime = (float)General/[[NSDate date] timeIntervalSinceDate:startDate];
    if (elapsedTime > nextInterval) {
        nextInterval = elapsedTime - fmodf(elapsedTime, interval) + interval;
        [movieView gotoBeginning:self];
        [movieView start:self];
    }
    if (!(displayCount++ % 5)) [self setNeedsDisplay:YES];
    if (!stopMetronome) [self performSelector:@selector(metronome) 
                            withObject:nil afterDelay:0.01f];
}

- (void)awakeFromNib {
    if (!tick) {
        NSURL *url = [NSURL fileURLWithPath:@"/System/Library/Sounds/Bottle.aiff"];
        tick = General/[[NSMovie alloc] initWithURL:url byReference:YES];
        if (tick) {
            movieView = General/[[NSMovieView alloc] initWithFrame:General/NSZeroRect];
            [movieView setMovie:tick];
        }
        General/NSLog(@"tick: %@", tick);
        [self sliderAction:bpmSlider];
        pi = 3.14159f;
        maxRadians = 3.0f * pi / 4.0f;
        minRadians = pi / 4.0f;
    }
}

- (General/IBAction)sliderAction:(id)sender {
    beatsPerMinute = [sender floatValue];
    interval = 60.0f / beatsPerMinute;
    doubleInterval = interval * 2.0f;
    nextInterval = elapsedTime - fmodf(elapsedTime, interval) + interval;
    [self setNeedsDisplay:YES];
}

- (General/IBAction)startStop:(id)sender {
    stopMetronome = General/sender title] isEqualToString:@"Stop"];
    if (stopMetronome) {
        [sender setTitle:@"Start"];
    } else {
        [sender setTitle:@"Stop"];
        [startDate release];
        startDate = [[[[NSDate date] retain];
        elapsedTime = (float)General/[[NSDate date] timeIntervalSinceDate:startDate];
        nextInterval = elapsedTime - fmodf(elapsedTime, interval) + interval;
        [self metronome];
    }
}

- (void)drawRect:(General/NSRect)rect {
	float doubleRemainder = fmodf(elapsedTime, doubleInterval) / doubleInterval;
	float fraction = (doubleRemainder < 0.5f) ? doubleRemainder / 0.5f 
                                                 : 1.0f - (doubleRemainder - 0.5f) / 0.5f;
	float sweep = maxRadians - minRadians;
	float position = minRadians + sweep * fraction;
	float x = cos(position);
	float y = sin(position);
	float length = rect.size.height / 2.0f;
	General/NSPoint p1 = General/NSMakePoint(General/NSMidX(rect), 0.0f);
	General/NSPoint p2 = General/NSMakePoint(p1.x + x * length, p1.y + y * length);
	General/NSBezierPath *bp = General/[NSBezierPath bezierPath];
	[bp moveToPoint:p1];
	[bp lineToPoint:p2];
	[bp stroke];
	General/NSString *elapsedTimeString = General/[NSString stringWithFormat:@"bpm: %.2f fraction: %.2f", beatsPerMinute, fraction];
	General/NSSize size = [elapsedTimeString sizeWithAttributes:nil];
	General/NSPoint p = General/NSMakePoint(General/NSMidX(rect) - size.width / 2.0f, 
                                    General/NSMaxY(rect) - size.height);
	[elapsedTimeString drawAtPoint:p withAttributes:nil];
}

@end



The rest is up to you to figure out. All of the pieces are there. Have fun!! --zootbobbalu

*Cool!!  I'm not the original poster, but I'd like to express my appreciation as well.  That's a nice example that I definitely want to go through when I get a chance.  I'm curious though - do you think it's more accurate to use performSelector:withObject:afterDelay: with a zero delay (and check the time) than it is to set it for the real time interval you're interested in?*

----

Yes, it would be more accurate to use zero delay, but that would hog CPU resources. I also think that the best thing to do would be to check out General/CoreAudio's timing API. 100 samples per second is enough precision for me. When I sampled 1000 times per second, CPU usage went up to about 15 percent on my machine. You don't have to update the display as often as you sample the elapsed time. 30 fps is enough to provide smooth motion. --zootbobbalu

----

*Actually, what I intended to ask was why don't you just just have the delay be the actual interval you want to wait for (e.g. 1 second) and avoid the extra calls and checks.  Do you think that what you actually did is more accurate?*

----

Oh I see what you're asking now. You know I don't know which method has more temporal correctness. If an General/NSTimer uses a global reference date to fire at a set interval, then the timer will probably be less overhead. If the timer's error is cumulative, then I think sampling a reference time interval is better. I really haven't tested the time correctness of either case. 

I still think that if accuracy is what you're after then I wouldn't even mess with General/NSTimers and I would start reading up on General/CoreAudio's timing API. --zootbobbalu

----

I just wrote a little test program (actually to test my usage of General/NSLock).  In my program I set a General/NSTimer to repeat at 0.001 seconds: it takes the lock, does a small operation, and relinquishes the lock.  I also launched a separate thread that does the same thing (same lock, same small piece of protected code) and then usleeps for 1000 microseconds.  I asked the main program and the thread to report results after 10000 iterations each.

    
current time = 10.0499  main ticks = 10000
current time = 10.9701  thread ticks = 10000
current time = 20.0499  main ticks = 20000
current time = 22.004 thread ticks = 20000


The timer is doing very well, it seems.  The thread is a bit slower because I asked it to do work and then usleep for 1000 microseconds.  This is on a Macbook Pro, Core 2 Duo, 2.5 General/GHz, with Mac OS X 10.6.5 - GM
