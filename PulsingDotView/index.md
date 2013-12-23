---
layout: page
title: PulsingDotView
---



Here is some code i wrote to play around with and experiment with animation using NSTimer.  I don't know much about efficient animation, so what you see here is probably nothing ideal.  But what the hell...

The Interface File:
    
@interface PulsingDotView : NSView {
    NSTimer *timer;
    NSColor *color;
    NSBezierPath *path;

    NSPoint center;
    double vy0, vx0, vx, vy, ax, ay, dt, tx, ty, x0, y0, radius;
}

- (void)changeDot;
- (BOOL)isOpaque;

@end


I orignally wrote this code to make a circle pulse in the view, but then after a substantial bit  of playing around it got changed into something else...

And the Implementation File
    
#import "PulsingDotView.h"
#import "math.h"

@implementation PulsingDotView

- (id)initWithFrame:(NSRect)frame {
    self = [super initWithFrame:frame];
    
    y0 = 200;
    x0 = 200;
    center.x = x0;
    center.y = y0;
    
    vy0 = 10;
    vx0 = 20;
    
    ay = -500;
    ax = 0;
    dt = .1;
    
    radius = 2;
    color = [[NSColor blueColor] retain];
    path = [[NSBezierPath bezierPath] retain];
    [path moveToPoint:center];
    
    timer = [NSTimer scheduledTimerWithTimeInterval:.001
                     target:self
                     selector:@selector(changeDot)
                     userInfo:nil
                     repeats:YES];
    return self;
}

- (void)drawRect:(NSRect)rect {    
    NSRect dotRect;

    [[NSColor whiteColor] set];
    NSRectFill([self bounds]);
        // Equiv to [[NSBezierPath bezierPathWithRect:[self
        //                                    bounds]] fill]

    dotRect.origin.x = center.x - radius;
    dotRect.origin.y = center.y - radius;
    dotRect.size.width  = 2 * radius;
    dotRect.size.height = 2 * radius;
    
    [color set];
    // if you uncomment this following line (and another similar one
    // further down) the ball will be trailed by a line
    //[path stroke];
    [[NSBezierPath bezierPathWithOvalInRect:dotRect] fill];
}

- (BOOL)isOpaque {
    return NO;
}

- (void)changeDot {    
    NSRect bounds;
    
    //These next four lines are an attempt to simulate some gravity in 
    // the view.  Standard kinematics...
    center.y = (y0) + (vy0 * ty) + (0.5 * ay * ty * ty);
    center.x = (x0) + (vx0 * tx) + (0.5 * ax * tx * tx);
    vy = (vy0) + (ay * ty);
    vx = (vx0) + (ax * tx);
    
    bounds = [self bounds];
    
    // These next four if-statements basically change the velocity of the
    // ball to create the bounce off of the walls.

    if ( (center.y + radius) >=  bounds.size.height ) {
        vy0 = -vy;
        ty = 0;
        y0 = bounds.size.height - radius;
    }

    if ( center.y <= radius ) {
        vy0 = -vy;
        ty = 0;
        y0 = radius;
    }
    
    if ( (center.x + radius) >=  bounds.size.width ) {
        vx0 = -vx;
        tx = 0;
        x0 = bounds.size.width - radius;
    }
    
    if ( center.x <= radius) {
        vx0 = -vx;
        tx = 0;
        x0 = radius;
    }
    
    tx += dt;
    ty += dt;

    // The other line to uncomment to have a line trail the bouncing ball
    //[path lineToPoint:center];
    [self setNeedsDisplay:YES];
}

@end


This view is just hooked up to a view in interface builder.  Have fun!

----

Would be better to only update the dirty area instead of the whole view. :/

Here's what I've tossed together...

The Interface Files:
    

@protocol Animation

- (BOOL)animates;
- (void)animateOneFrame;

@end


@interface AnimatingView : NSView
{
    NSTimer *animTimer;
}

- (void)animateOneFrame;

@end



Implementation File:
    
@implementation AnimatingView

- (void)dealloc
{
     [animTimer invalidate]; // Don't need this anymore
[super dealloc];
}

- (id)initWithFrame:(NSRect)rect
{
    self = [super initWithFrame:rect];
        
         animTimer = [NSTimer scheduledTimerWithTimeInterval:0.001 target:self selector:@selector(animateOneFrame) userInfo:nil repeats:YES];

    return self;
}

- (void)animateOneFrame
{
    NSArray *subs = [self subviews];
        id view;
        int i=0;

            for (;i<[subs count];i++) {
                view = [subs objectAtIndex:i];
                    
                    if ([view conformsToProtocol:@protocol(Animation)])
                        if ([view animates])
                                [view animateOneFrame];
            }
}
@end


I set my window's contentView to be an instance of AnimatingView and load on some custom views/controls that conform to the Animation protocol.


Here's an example of a view that conforms to the Animation protocol (Compiled here);
    

#import "Animation.h"

@interface BouncingView : NSView <Animation>
{
     float verticalSpeed;
}
- (BOOL)animates;
- (void)animateOneFrame;
@end


// BouncingView.m...

@implementation BouncingView

- (void)awakeFromNib
{
	verticalSpeed = 0.9; // whatever.
}

- (BOOL)animates
{
      // if (someCondition) return NO; 

    return YES;
}

- (void)animateOneFrame
{
	NSRect frame = [self frame];
        NSRect oldFrame = frame;
	id superview = [self superview];

	if (frame.origin.y <= 0) { verticalSpeed = verticalSpeed*-1; }
	if (frame.origin.y+frame.size.height >= NSMaxY([superview frame])) { verticalSpeed = verticalSpeed*-1; }

 	frame.origin.y += verticalSpeed;
 	[self setFrameOrigin:frame.origin];

         // erase where we were..
        [superview setNeedsDisplayInRect:oldFrame];
         // draw where we are..
        [superview setNeedsDisplayInRect:frame];
}
@end

