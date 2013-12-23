---
layout: page
title: DockIconCounter
---

How do you have that little image by the icon, like in Mail?

----

I don't know how the question was aimed at, but I'm pretty sure its an NSBezierPath as a circle in red with a 2 in it. Well, at least thats what I do. -- MatPeterson

Actually, I believe it's an image from within the bundle, with the text applied to it. As for how you apply it to the dock icon, I really don't recall; you might begin looking in NSApplication's documentation. -- RobRix

----

**Executive summary of the example code below:**

* Create an NSImage of your icon and overlay that number image on top of it
* Use NSApplication's setApplicationIconImage: to put that icon into the dock
* PROFIT!

----

Also see BezierPathForDockIconCountBadge

----

**Controller Header**

    
#import <Cocoa/Cocoa.h>

@interface IconImageCounterController : NSObject
{
    NSImage *iconImageBuffer;
    NSImage *originalIcon;
    NSTimer *timer;
    NSDictionary *attributes;
    int count;
}
- (void)drawCountdownIcon:(int)number;
- (void)timerAction:(NSTimer *)_timer;
@end


**Controller Source**

    
#import "IconImageCounterController.h"

@implementation IconImageCounterController

- (void)awakeFromNib {
    if (timer) return;
    originalIcon = [[NSApp applicationIconImage] copy];
    iconImageBuffer = [originalIcon copy];
    timer = [[NSTimer scheduledTimerWithTimeInterval:1
                                                target:self
                                                selector:@selector(timerAction:)
                                                userInfo:nil
                                                repeats:YES] retain];
    attributes = [[NSDictionary alloc] initWithObjectsAndKeys:
                        [NSFont fontWithName:@"Helvetica-Bold" size:26], NSFontAttributeName,
                        [NSColor whiteColor], NSForegroundColorAttributeName, nil];
        
    count = 20;
}

- (void)dealloc {
    [originalIcon release];
    [iconImageBuffer release];
    [attributes release];
    [super dealloc];
}

- (void)timerAction:(NSTimer *)_timer {
    if (count < 0 && timer == _timer) {[timer invalidate]; [timer release]; timer = nil; return;}
    [self drawCountdownIcon:count--];
    
}

- (void)drawCountdownIcon:(int)number {
    
    NSString *countdown = [NSString stringWithFormat:@"%i", number];
    NSSize numSize = [countdown sizeWithAttributes:attributes];
    NSSize iconSize = [originalIcon size];
    if (number) {
        [iconImageBuffer lockFocus];
        [originalIcon drawAtPoint:NSMakePoint(0, 0)
            fromRect:NSMakeRect(0, 0, iconSize.width, iconSize.height) 
            operation:NSCompositeSourceOver 
            fraction:1.0f];
        float max = (numSize.width > numSize.height) ? numSize.width : numSize.height;
        max += 16;
        NSRect circleRect = NSMakeRect(iconSize.width - max, iconSize.height - max, max, max);
        NSBezierPath *bp = [NSBezierPath bezierPathWithOvalInRect:circleRect];
        [[NSColor colorWithCalibratedRed:0.8f green:0.0f blue:0.0f alpha:1.0f] set];
        [bp fill];
        [countdown drawAtPoint:NSMakePoint(NSMidX(circleRect) - numSize.width / 2.0f, 
                                            NSMidY(circleRect) - numSize.height / 2.0f + 2.0f) 
                    withAttributes:attributes];
        
        [iconImageBuffer unlockFocus];
        [NSApp setApplicationIconImage:iconImageBuffer];
    }
    else [NSApp setApplicationIconImage:originalIcon];
    
}




-- zootbobbalu


You Rock!  -EcumeDesJours

----

I found a very nice implementation available free here: http://blog.oofn.net/2006/01/08/badging-for-everyone/ --GrahamCox

----

Here's my implementation of a dock badge (like Acquisition's): http://johndevor.wordpress.com/2007/01/17/dock-badging-is-fun/ --JohnDevor

----

If you're using Leopard you can do the following: 

[[NSApp dockTile] setBadgeLabel:@"1000"];

Check out the source of  Transmission -- http://www.transmissionbt.com/ , the files Badger and BadgerView, if you need to do custom badge work or support pre-leopard machines.

MatthieuCormier

