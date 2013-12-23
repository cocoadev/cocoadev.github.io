---
layout: page
title: TimerFailsToRepeat
---

Having declared lightTimer in my header:

    
- (void)awakeFromNib
{
General/NSNOtificationCenter *nc = General/[NSNotificationCenter defaultCenter];
[nc addObserver:self selector:@selector(timerStart) name:@"General/TimerFire" object:nil];
}

- (void)timerStart
{
double p = 1;
lightTimer = General/[[NSTimer scheduledTimerWithTimeInterval:(p) target:self selector:@selector(lightTimer) userInfo:nil repeats:YES] retain];
[lightTimer fire];
}


Now, I know I get the notification as General/NSLog previously indicated it, and I know my timer is getting set up and the [lightTimer fire]; is firing it, but it is not repeating. When I put a [self timerStart]; in the awakeFromNib the timer runs properly. Is there something I am doing wrong or is there a bug where this code will not run when called from a notification? (Yes lightTimer exists).

----

Hmmm... The first General/NSTimer implementation I made worked correctly and looked like this :

    

temps = General/[NSTimer scheduledTimerWithTimeInterval:1.000000
                             target:self
                             selector:@selector(avTemps:)
                             userInfo:nil
                             repeats:YES ];



Then, there's the avTemps method which contains whatever I want and invalidate the General/NSTimer after a condition is reached.
In Apple's reference docs, it says the selector should be the variable that represents the timer itself, in your case, lightTimer.
However, my piece of code works perfectly and I didn't follow that rule...
