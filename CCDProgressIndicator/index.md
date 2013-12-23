---
layout: page
title: CCDProgressIndicator
---

CCDProgressIndicator is a simple subclass for drawing rounded progress bars.

    
// CCDProgressIndicator.h
#import <AppKit/AppKit.h>

typedef enum {
    CCDProgressIndicatorSquareBezel  = NSTextFieldSquareBezel,
    CCDProgressIndicatorRoundedBezel = NSTextFieldRoundedBezel
} CCDProgressIndicatorBezelStyle;


@interface CCDProgressIndicator : NSProgressIndicator
{
    NSTextFieldCell *textCell;
    NSImage *scratch;
}

- (void)setBezelStyle:(CCDProgressIndicatorBezelStyle)style;
- (CCDProgressIndicatorBezelStyle)bezelStyle;
@end

    
// CCDProgressIndicator.m
#import "CCDProgressIndicator.h"

@implementation CCDProgressIndicator

- (id)initWithCoder:(NSCoder*)coder {
    self = [super initWithCoder:coder];
    if (self) {
        [self setBezeled:YES];
        textCell = [[NSTextFieldCell alloc] initTextCell:@""];
        [textCell setControlSize:[self controlSize]];
        [textCell setDrawsBackground:NO];
        [textCell setBezeled:YES];
        
        scratch = [[NSImage alloc] initWithSize:[self bounds].size];
        [scratch setFlipped:YES];

        // yuck.
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(setNeedsDisplay:) name:NSWindowDidBecomeMainNotification object:[self window]];
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(setNeedsDisplay:) name:NSWindowDidResignMainNotification object:[self window]];
    }

    return self;
}

- (void)dealloc
{
    [[NSNotificationCenter defaultCenter] removeObserver:self];
    [textCell release];
    [scratch release];
    [super dealloc];
}

- (void)setBezelStyle:(CCDProgressIndicatorBezelStyle)style
{
    [textCell setBezelStyle:style];
    [self setNeedsDisplay:YES];
}
- (CCDProgressIndicatorBezelStyle)bezelStyle
{
    return [textCell bezelStyle];
}

- (void)drawRect:(NSRect)rect
{
    if ([self bezelStyle] == NSTextFieldRoundedBezel && [self style] == NSProgressIndicatorBarStyle) {
    NSImage *progress = [[NSImage alloc] initWithSize:rect.size];
    NSImage *textImage = [progress copy];
    NSRect tRect = rect;
        tRect.origin.x += 2;
        tRect.size.width -= 4;
        tRect.size.height -= 4;
        
    [progress setFlipped:YES];
    [textImage setFlipped:YES];

    [textImage lockFocus];
    [textCell drawWithFrame:rect inView:[NSView focusView]];
    [textImage unlockFocus];

    [progress lockFocus];
    [super drawRect:rect];
    [progress unlockFocus];
    
    [scratch setSize:rect.size];
    [scratch lockFocus];
        [[NSColor whiteColor] set];
        NSRectFill(rect); // clear scratch of any previous drawing
    [progress drawAtPoint:NSMakePoint(0,0) fromRect:tRect operation:NSCompositeSourceOver fraction:0.8];
    [textImage drawAtPoint:NSMakePoint(0,0) fromRect:rect operation:NSCompositeDestinationAtop fraction:1];
    
    [scratch unlockFocus];
    
    // draw it all
    [scratch drawAtPoint:NSMakePoint(0,0) fromRect:rect operation:NSCompositeSourceOver fraction:1];
    [progress release];
    [textImage release];
    } else [super drawRect:rect];
}

@end

----
1/16; Don't know how I overlooked it but *scratch* wasn't being cleared before, now it is.

4/8; CCDImageCategory wasn't needed, as described on its page.

