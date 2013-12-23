---
layout: page
title: MouseEnteredTroubleWithNSView
---



Hey guys/girls,

I've been trying to get an NSView to respond to the mouseEntered event but having a lot of trouble. I've read apple's docs and followed their steps but I think I must have missed something really quite obvious. Anyhow if it is possible, could someone paste some example code in or tell me the little trick in getting an NSView to respond to mouseEntered.

This is what I've got so far:


* A window (subclassed)
* A custom view (subclassed)


My window does accept mouse moved events and also does not ignore them.
Here is the .m file of my window subclass

    
#import "mouseServer.h"

@implementation mouseServer

//In Interface Builder we set CustomWindow to be the class for our window, so our own initializer is called here.
- (id)initWithContentRect:(NSRect)contentRect styleMask:(unsigned int)aStyle backing:(NSBackingStoreType)bufferingType defer:(BOOL)flag {
    NSWindow* result = [super initWithContentRect:contentRect styleMask:NSBorderlessWindowMask backing:NSBackingStoreBuffered defer:NO];
    [result setBackgroundColor: [NSColor clearColor]];
    [result setLevel: NSNormalWindowLevel];
    [result setAlphaValue:1.0];
    [result setOpaque:NO];
    [result setHasShadow: YES];
  	[self setAcceptsMouseMovedEvents:YES];
	[self setIgnoresMouseEvents:NO];
	return result;
}

- (BOOL) canBecomeKeyWindow
{
    return YES;
}

@end


My custom view has had a trackingRect assigned, does accept first responder and has the method -(void)mouseEntered:(NSEvent *)theEvent
Here is the .m file of my custom view subclass

    
#import "mouseServerCV.h"

@implementation mouseServerCV

- (id)initWithFrame:(NSRect)frameRect
{
	if ((self = [super initWithFrame:frameRect]) != nil) {
		// Add initialization code here
	trackingTag = [self addTrackingRect:[self visibleRect] owner:[self window] userData:nil assumeInside:NO];
	}
	return self;
}

- (void)drawRect:(NSRect)rect
{
[[NSColor colorWithCalibratedHue:0 saturation:0 brightness:0 alpha:0.8] set];
NSRectFill([self frame]);
}

- (BOOL)acceptsFirstResponder
{
return YES;
}

-(void)mouseEntered:(NSEvent *)theEvent
{
NSLog(@"VIEW LOGGED");
}
@end


BTW, if your wondering why i'm doing this, its because I need to create a hotcorner for my app. I achieved the correct behavior before by using applescript studio and having a small transparent window corner in the corner of the screen. If you think I might be wasting my time doing it this way and know of a better or more civilised way of creating hotcorner functionality please feel free to suggest!

Thanks!

Cheers
Louis

----

At the time     initWithFrame: is called,  your view hasn't been added to the window yet. Since it's the window that keeps track of tracking rects, wait until     viewDidMoveToWindow is called before calling     addTrackingRect:.

     
- (void) viewDidMoveToWindow {
	trTag = [self addTrackingRect:[self bounds]
				owner:self
				userData:nil
				assumeInside:NO];
}
 
----
One of your problems is your     initWithContentRect:::: method -- you never set     self to     result, and so you don't call     setAcceptsMouseMovedEvent: and     setIgnoresMouseEvents: on the right object. Here's (theoretically) correct version of the method, which I hope works out for you. --JediKnil
    
- (id)initWithContentRect:(NSRect)contentRect styleMask:(unsigned int)aStyle backing:(NSBackingStoreType)bufferingType defer:(BOOL)flag {
    if (self = [super initWithContentRect:contentRect styleMask:NSBorderlessWindowMask backing:NSBackingStoreBuffered defer:NO]) {
        // These should be done in IB, if possible
        [self setBackgroundColor: [NSColor clearColor]];
        [self setLevel: NSNormalWindowLevel];
        [self setAlphaValue:1.0];
        [self setOpaque:NO];
        [self setHasShadow: YES];

       // These are probably needed here
        [self setAcceptsMouseMovedEvents:YES];
        [self setIgnoresMouseEvents:NO];
    }
    return self;
}
 
----
Wow! That worked a treat. Placing the custom window, and custom view in any corner of the screen + 1 pixel in height and width gives you an instant hotcorner.
Thanks everyone!
Cheers, Louis
----

