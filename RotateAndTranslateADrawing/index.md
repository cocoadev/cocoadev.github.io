---
layout: page
title: RotateAndTranslateADrawing
---



Here's an example that shows how to spin an object centered about a point in a view. 

**SpinnerView.h**
    
#import <Cocoa/Cocoa.h>

@interface SpinnerView : NSView
{
    float rad;
}
@end


**SpinnerView.m**
    
#import "SpinnerView.h"

@implementation SpinnerView

- (id)initWithFrame:(NSRect)frameRect
{
	if ((self = [super initWithFrame:frameRect]) != nil) {
		// Add initialization code here
	}
	return self;
}

- (float)slopeInRadiansFromVector:(NSSize)vector {
    float pi = 3.14159265359f;
    float vMag = sqrt(vector.width * vector.width + vector.height * vector.height); 
    return (vector.height >= 0.0f) ? acos(vector.width / vMag) : 2 * pi - acos(vector.width / vMag);
}

- (void)drawLeftRightCursorWithRotation:(float)rotation {
    
    /////////////////////////////////////////////////////////////////
    //
    // arrow dimensions and drawing math
    //
    /////////////////////////////////////////////////////////////////
    
    float dim = 32.0;                               // overall length of double headed arrow
    float halfDim = dim / 2.0f;                     // half arrow length
    float triDim = dim / 3.5f;                      // arrow head dimension
    float inset = 1.0f;                             // arrow outline width
    float insetFactor = 1.0f + sqrt(2.0f);          // right triangle factor
    NSPoint left = NSMakePoint(-halfDim, 0.0f);     // point of left arrow head
    NSPoint right = NSMakePoint(halfDim, 0.0f);     // point of right arrow head

    // where the drawing origin will be translated (origin translated to center of view)
    NSRect visibleRect = [self visibleRect];
    NSPoint offset = NSMakePoint(NSMidX(visibleRect), NSMidY(visibleRect));
    
    /////////////////////////////////////////////////////////////////
    //
    // rotate and translate graphics state
    //
    /////////////////////////////////////////////////////////////////
    
    // remember the current graphics state
    [[NSGraphicsContext currentContext] saveGraphicsState];
    
    // translate and rotate graphics state (order is important, translate first before rotating)
    NSAffineTransform *transform = [NSAffineTransform transform];
    [transform translateXBy:offset.x yBy:offset.y];
    [transform rotateByRadians:rotation];
    [transform concat];
    
    /////////////////////////////////////////////////////////////////
    //
    // resizeLeftRightCursor drawing centered at origin
    //
    /////////////////////////////////////////////////////////////////

    NSBezierPath *bp = [NSBezierPath bezierPath];
    [[NSColor whiteColor] set];
    // arrow shaft background
    [bp moveToPoint:NSMakePoint(left.x + triDim - inset, left.y)];
    [bp lineToPoint:NSMakePoint(right.x - triDim + inset, right.y)];
    [bp setLineWidth:inset * 3.0f];
    [bp stroke];
    // left arrowhead background
    [bp removeAllPoints]; [bp moveToPoint:left];
    [bp lineToPoint:NSMakePoint(left.x + triDim, triDim)];
    [bp lineToPoint:NSMakePoint(left.x + triDim, -triDim)];
    [bp lineToPoint:left];
    [bp fill];
    // right arrowhead background
    [bp removeAllPoints]; [bp moveToPoint:right];
    [bp lineToPoint:NSMakePoint(right.x - triDim, triDim)];
    [bp lineToPoint:NSMakePoint(right.x - triDim, -triDim)];
    [bp lineToPoint:right];
    [bp fill];
    [[NSColor blackColor] set];
    // left arrowhead
    [bp removeAllPoints]; [bp moveToPoint:NSMakePoint(left.x + inset * 1.414, left.y)];
    [bp lineToPoint:NSMakePoint(left.x + triDim - inset, triDim - inset * insetFactor)];
    [bp lineToPoint:NSMakePoint(left.x + triDim - inset, inset * insetFactor - triDim)];
    [bp lineToPoint:NSMakePoint(left.x + inset * 1.414, left.y)];
    [bp fill];
    // right arrowhead
    [bp removeAllPoints]; [bp moveToPoint:NSMakePoint(right.x - inset * 1.414, right.y)];
    [bp lineToPoint:NSMakePoint(right.x - triDim + inset, triDim - inset * insetFactor)];
    [bp lineToPoint:NSMakePoint(right.x - triDim + inset, inset * insetFactor - triDim)];
    [bp lineToPoint:NSMakePoint(right.x - inset * 1.414, right.y)];
    [bp fill];
    // arrow shaft 
    [bp removeAllPoints];
    [bp moveToPoint:NSMakePoint(left.x + triDim - inset, left.y)];
    [bp lineToPoint:NSMakePoint(right.x - triDim + inset, right.y)];
    [bp setLineWidth:inset];
    [bp stroke];

    // leave the graphics state as you found it
    [[NSGraphicsContext currentContext] restoreGraphicsState];
}

- (void)drawRect:(NSRect)rect
{
    [[NSColor blueColor] set]; NSRectFill(rect);
    BOOL animate = YES;
    if (animate) rad += 0.1f;
    else rad = [self slopeInRadiansFromVector:NSMakeSize(1.0f, 1.0f)];
    [self drawLeftRightCursorWithRotation:rad];
    [self performSelector:@selector(display) withObject:nil afterDelay:0.01f];

}
    
@end


This code gives you the option to animate the spinning motion of a double headed arrow about an offset point or to simply rotate the double headed arrow about an offset point. The offset point is currently set to the center of the view, but you can easily change this location to any point you wish by changing the line:
    
    NSPoint offset = NSMakePoint(NSMidX(visibleRect), NSMidY(visibleRect));

If you set the "animate" variable to "NO", you will set the amount the drawing will be rotated to a fixed angle associated with the slope of a vector. The line 
    
    else rad = [self slopeInRadiansFromVector:NSMakeSize(1.0f, 1.0f)];
 
sets the rotation (in radians) to pie over four (i.e. 45 degrees). 


--zootbobbalu

----

Nice code. Puts a new 'spin' on an old cursor. But..."pie over four"? That takes the cake. A little half-baked, in other words.
Are you completely crustworthy?

----

LOL!! that's pretty funny!! Just making sure everyone was paying attention ;-)

--zootbobbalu

----

Here's a simple example if the example above is hard to understand

**SpinnerView.h**
    
#import <Cocoa/Cocoa.h>

@interface SpinnerView : NSView
{
    float rad;
}
@end


**SpinnerView.m**
    
#import "SpinnerView.h"

@implementation SpinnerView

- (void)drawRect:(NSRect)rect
{

    [[NSColor blueColor] set]; NSRectFill(rect);
    rad += 0.1f;
    
    // make a rectangle centered about the origin
    float width = 100.0f, height = 50.0f;    
    NSRect greenRect = NSMakeRect(-width / 2.0f, -height / 2.0f, width, height);
    
    // remember the current graphics state
    [[NSGraphicsContext currentContext] saveGraphicsState];

    // where the drawing origin will be translated (origin translated to center of view)
    NSRect visibleRect = [self visibleRect];
    NSPoint offset = NSMakePoint(NSMidX(visibleRect), NSMidY(visibleRect));

    NSAffineTransform *transform = [NSAffineTransform transform];

    // translate and rotate graphics state (order is important, translate first before rotating)
    [transform translateXBy:offset.x yBy:offset.y];
    [transform rotateByRadians:rad];
    [transform concat];

    // draw a green rectangle that has been translated by "offset" and rotated "rad" radians 
    [[NSColor greenColor] set];
    NSRectFill(greenRect);

    // leave the graphics state as you found it
    [[NSGraphicsContext currentContext] restoreGraphicsState]; 

    [self performSelector:@selector(display) withObject:nil afterDelay:0.01f];

}

@end



--zootbobbalu

----
See also QuartzShapeDrawing

Warning: Both above examples use
    [self performSelector:@selector(display) withObject:nil afterDelay:0.01f];

I don't think it is strictly correct to perform a method, -display, that does not take an argument but pass an argument anyway.

----
It's also a really bad way to do animation. The correct way would be to create an NSTimer to call a method which calls     setNeedsDisplay:YES. That way you can do things like easily cancel the animation, change its rate, etc.

----

From Apple's documentation on     performSelector:withObject:afterDelay: ->
*
Sends an aSelector message to the receiver sometime after delay. This method returns before the aSelector message is sent. The aSelector method should not have a significant return value and should take a single argument of type id, or no arguments; anArgument will be the argument passed in the message, or nil if the method does not take any arguments. Note that self and anArgument are retained until after the message is sent. See �Selectors� for a description of the SEL type.
*

I agree that a timer would be better. I wrote this example code mainly to demonstrate how to use transforms and before I figured out that you don't have to retain an NSTimer object (the run loop will retain the timer object for you after it has been added). Normally I use timers to animate things, so I do agree that calling     setNeedsDisplay:YES and using a timer is the proper way to animate.

One side note on     performSelector:withObject:afterDelay:. You can cancel a delayed method call with     cancelPreviousPerformRequestsWithTarget:selector:object: or     cancelPreviousPerformRequestsWithTarget:. This is nice, because you can filter delayed method calls by using the single argument as a key. 

    
- (void)awakeFromNib {

    [self performSelector:@selector(delayedAction:) withObject:[NSNumber numberWithFloat:1.0f] afterDelay:1.0f];
    [self performSelector:@selector(delayedAction:) withObject:[NSNumber numberWithFloat:2.0f] afterDelay:2.0f];

}

- (void)delayedAction:(NSNumber *)delayValue {

    [self cancelPreviousPerformRequestsWithTarget:self selector:@selector(delayedAction:) object:delayValue];
    [self performSelector:@selector(delayedAction:) withObject:delayValue afterDelay:[delayValue floatValue]];

}
 

Since you have the freedom to choose the selector, target and the single argument and the ability to cancel delayed requests based on the arguments used to create the request, using     performSelector:withObject:afterDelay: and     cancelPreviousPerformRequestsWithTarget:selector:object: is a convenient way to organize timed actions. Animations that require high precision should stick to using NSTimers, but all other cases are well suited for using     performSelector:withObject:afterDelay:. 
 
--zootbobbalu

