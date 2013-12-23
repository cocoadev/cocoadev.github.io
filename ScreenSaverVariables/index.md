---
layout: page
title: ScreenSaverVariables
---

I want to make a SIMPLE screen saver with a SIMPLE NSBezierPath. I got the path, I got a color, but I can't get variables that will make my shape move. Here is my code so far :

    

// Balle.h

#import <Foundation/Foundation.h>
#import <AppKit/AppKit.h>
#import <ScreenSaver/ScreenSaver.h>

@interface BalleView : ScreenSaverView 
{
    NSBezierPath *balle;
    NSRect rect;
    NSColor *couleur;

    int x1; int y1;
    int x2; int y2;
}

@end



And my implementation :

    

#import "BalleView.h"

@implementation BalleView

- (id)initWithFrame:(NSRect)frame isPreview:(BOOL)isPreview
{
    self = [super initWithFrame:frame isPreview:isPreview];
    if (self) {
        [self setAnimationTimeInterval:1/10.0];
        
        x1 = 50; y1 = 50;
        x2 = 30; y2 = 30;
        
        balle = [[NSBezierPath bezierPathWithOvalInRect:NSMakeRect(x1,y1,x2,y2)] retain];
        couleur = [NSColor colorWithCalibratedRed:1.0
                                            green:0 	
                                             blue:0 
                                            alpha:1.0];

    }
    return self;
}

- (void)animateOneFrame
{
    [couleur set]; [balle fill];
    
    x1++; y1++;
}

@end



----
MacEdition has a screensaver article at http://macedition.com/bolts/bolts_20020514.php

The basic thing you are overlooking is you are not regenerating the bezier curve after you change the control points. This doesn't happen automatically. Try this instead:

    

// Balle.h

#import <Foundation/Foundation.h>
#import <AppKit/AppKit.h>
#import <ScreenSaver/ScreenSaver.h>

@interface BalleView : ScreenSaverView 
{
    /* NSBezierPath *balle; No need for this ivar */
    NSRect rect;
    NSColor *couleur;

    int x1; int y1;
    int x2; int y2;
}

@end

// Balle.m
#import "BalleView.h"

@implementation BalleView

- (id)initWithFrame:(NSRect)frame isPreview:(BOOL)isPreview
{
    self = [super initWithFrame:frame isPreview:isPreview];
    if (self) {
        [self setAnimationTimeInterval:1/10.0];
        
        x1 = 50; y1 = 50;
        x2 = 30; y2 = 30;
        
        // you must promise to always retain autoreleased values! the fact that
        // this didn't crash is an implementation detail, and not guaranteed by
        // AppKit, or anyone else ... 
        couleur = [[NSColor colorWithCalibratedRed:1.0
                                            green:0 	
                                             blue:0 
                                            alpha:1.0] retain];

    }
    return self;
}

- (void)dealloc
{
        [couleur release];
        [super dealloc];
}

- (void)animateOneFrame
{
    [couleur set]; 
    [[NSBezierPath bezierPathWithOvalInRect:NSMakeRect(x1,y1,x2,y2)] fill];

    
    x1++; y1++;
}

@end



Remember that method dispatches are just function calls. You wouldn't expect:

    
int x = 7;
printf("x = %d\n", x);
x = 10;


to print "x = 10" would you? That's what you were expecting in your example above.

-- MikeTrent

----

There's still something I need to know. When I try the code, the ball doesn't really move, it actually copies itself in the direction I give to it with x and y. That's why I intended to make the NSBezierPath a more global variable. I want the ball to move and not leave a trail of the same color behind it...

----

When you draw to a view, you have complete control of the bits on screen. That means you are responsible for all of the animation. if you want the ball to move, you must move the ball. The most simple way to do this is to re-draw your entire window every time:

    
- (void)animateOneFrame
{
    // erasing everything is very slow. but I'm lazy ... 
    [[NSColor blackColor] set];
    NSRectFill([self bounds]);

    [couleur set]; 
    [[NSBezierPath bezierPathWithOvalInRect:NSMakeRect(x1,y1,x2,y2)] fill];

    x1++; y1++;
}


But you are actually touching all the bits in your view every time, and that can get expensive (unless you're using OpenGL ...). It's better to limit the area you are updating. For example, you could paint the old ball in black, move x and y, and paint the new ball in couleur; next time through the animation cycle you'll end up drawing black over the new ball, moving x and y again, and so on. But drawing circles is expensive too, more so than drawing a square of the same size. The following is a good compromise:

    
- (void)animateOneFrame
{
    NSRect circleRect;

    // out with the old...
    [[NSColor blackColor] set];
    circleRect = NSMakeRect(x1,y1,x2,y2);
    NSRectFill(circleRect);

    // ...in with the new
    [couleur set]; 
    x1++; y1++;
    circleRect = NSMakeRect(x1,y1,x2,y2);
    [[NSBezierPath bezierPathWithOvalInRect:circleRect] fill];
}


This is probably *the* most basic computer animation technique, and there are other techniques for doing reasonable 2D animation. You might try poking around game programming web sites for a good overview or tutorial on 2D animation techniques. A book I found fairly helpful (although is pre-Carbon, and may be out of print) was Tricks of the Mac Game Programming Gurus. Or maybe someone here will kindly write a 2D animation primer.

-- MikeTrent

----

If the original poster wants some screensaver code that stores state between frames, and does some simple animation, they're welcome to steal from my Borkware Drip screensaver at http://borkware.com/products/borkware-drip/ .  Source code is available.

-- MarkDalrymple


One thought: does it really work anymore to erase the old circle by simply painting over the background colour? Doesn't quartz' antialiasing leave traces behind? -- EnglaBenny

If you're seeing artifacts, make the rectangle you're erasing to be a little larger, so it will erase any antialiasing left behind. -- someone

Yes, and yes. So maybe do this:

    
- (void)animateOneFrame
{
    NSRect circleRect;

    // out with the old...
    [[NSColor blackColor] set];
    circleRect = NSMakeRect(x1,y1,x2,y2);
    circleRect = NSInsetRect(circleRect, -1, -1);
    NSRectFill(circleRect);

    // ...in with the new
    [couleur set]; 
    x1++; y1++;
    circleRect = NSMakeRect(x1,y1,x2,y2);
    [[NSBezierPath bezierPathWithOvalInRect:circleRect] fill];
}


Drawing a rect is definitely faster than drawing a circle, anti-aliasing or no. -- MikeTrent

----

Actually, I've found that things move along quite nicely if you render the object (ball, in this case) into an NSImage, and then just tell the image to draw itself on the screen.  For example:

    

- (void) startAnimation {
    NSImage* img = [[NSImage alloc] initWithSize:NSMakeSize(100, 100)]];
    [img lockFocus]; 

    // Perform drawing stuff, just like normal...

    [img unlockFocus];

    // Now stick the image somwhere that you get at it later. I'll assume it's just a
    // member of this class.
    [self setImage:img];
}

- (void) animateOneFrame {
    NSPoint p = [self figureOutWhereToPutTheBall];
    NSImage* img = [self image];

    [img compositeToPoint:p operation:NSCompositeSourceOver];
}



If you do it this way, you don't have the expense of recalculating the bezier path at all... you just copy the pixels onto the screen!  You'll still have to clear the screen as previously stated, however.
-- AndrewMiner

