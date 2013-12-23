---
layout: page
title: ScreenSaverOneTimeDraw
---

I'm trying to do something very simple with a screensaver, yet I can't figure how to make it work.

I want my screensaver to draw a blue rectangle (see below) on screen at the beginning, and never redraw it afterwards. I tried to put it in initWithFrame, in animateOneFrame, in startAnimation, nothing seems to work.
    
    float b = SSRandomFloatBetween( 0.6 , 1.0 );
    [[NSColor colorWithDeviceRed:0.5 green:0.5 blue:b alpha:1.0] set];
    [[NSBezierPath bezierPathWithRect:lim] fill];


If I put the piece of code in the init, it makes the screen flash blue and grey continuously. Can anyone see the problem here ?

-- Trax

----

If it's flashing blue and grey, you probably put the code in     animateOneFrame, not the constructor. When the constructor is called, the focus isn't locked on the view, so drawing the NSBezierPath shouldn't do anything. You should do all your drawing in     drawRect: or     animateOneFrame, where the focus will be locked. (The focus isn't locked in     startAnimation: or     stopAnimation: either.) The     drawRect: method is called once when the ScreenSaverView is first drawn, then     animateOneFrame is called repeatedly. So if you want to draw the random bluish grey rectangle at the beginning, just put your code in     drawRect:. And remember, the superclass'     drawRect: fills the screen with black.

Also note that, if gamma fade is enabled, that first     drawRect: will occur while the screen is faded to black, so it will fade back up to your bluish rectangle.

----

The correct, Cocoa-friendly, state-engine-friendly way to do this is to define the following in your ScreenSaverView subclass:

    
- (void)startAnimation
{
    // pick an initial value for blueValue
    blueValue = SSRandomFloatBetween( 0.6 , 1.0 );

    // pick an initial value for lim
    lim = NSMakeRect(0, 0, 100, 100); // I dunno ... 
}

- (void)drawRect:(NSRect)rect
{
    [[NSColor colorWithDeviceRed:0.5 green:0.5 blue:blueValue alpha:1.0] set];
    [[NSBezierPath bezierPathWithRect:lim] fill];
}

- (void)animateOneFrame
{
        blueValue = SSRandomFloatBetween( 0.6 , 1.0 );
        [self setNeedsDisplay:YES];
}


By keeping all the drawing code in drawRect: you ensure it is always drawn when the Cocoa system needs it drawn. By keeping all of your computational logic out of the draw path, you ensure multiple-redraws don't cause unwanted side-effects in your animation. In this case, changing colors early isn't a big deal perhaps, but in a sophisticated state engine unexpected recomputation might be expensive or might cause glitchy behavior. 

-- MikeTrent

----

    
[self setNeedsDisplay:YES];


The above does not seem to work in a screenSaverView, it does not call drawRect at any point :-/

*yes it does. what does your code look like?*

