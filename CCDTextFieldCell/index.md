---
layout: page
title: CCDTextFieldCell
---

The cell CCDTextField uses to get things done.

    
// CCDTextFieldCell.h
#import <AppKit/AppKit.h>
#import "CCDPTextView.h"
#import "NSBezierPathCategory.h"


@interface CCDTextFieldCell : NSTextFieldCell
{
        NSButtonCell *leftCell;
        NSButtonCell *rightCell;

        NSColor *labelColor;

        NSImage *scratch;
        BOOL isAnimating;
}

- (void)setLabelColor:(NSColor *)lColor;
- (NSColor *)labelColor;

- (void)setAnimating:(BOOL)animating;
- (BOOL)isAnimating;

// modeled after NSSearchFieldCell
- (void)setLeftButtonCell:(id)cell;
- (id)leftButtonCell;

- (void)setRightButtonCell:(id)cell;
- (id)rightButtonCell;

- (NSRect)leftButtonRectForBounds:(NSRect)bounds;
- (NSRect)rightButtonRectForBounds:(NSRect)bounds;


// These return the same rect.
- (NSRect)titleRectForBounds:(NSRect)bounds; // inherited from NSCell but never called
- (NSRect)textRectForBounds:(NSRect)bounds; // modeled after NSSearchFieldCell

- (NSRect)labelRectForBounds:(NSRect)bounds; // must support labels

@end


    
// CCDTextFieldCell.m
#import "CCDTextFieldCell.h"

@implementation CCDTextFieldCell

- (id)initTextCell:(NSString *)txt
{
    if (self = [super initTextCell:txt]) {
    
        leftCell = [[NSButtonCell alloc] initImageCell:nil];
        rightCell = [[NSButtonCell alloc] initImageCell:nil];

            [leftCell setButtonType:NSMomentaryChangeButton];
            [leftCell setBezelStyle:NSRegularSquareBezelStyle];
            [leftCell setBordered:NO];
            [leftCell setImagePosition:NSImageOnly];

            [rightCell setButtonType:NSMomentaryChangeButton];
            [rightCell setBezelStyle:NSRegularSquareBezelStyle];
            [rightCell setBordered:NO];
            [rightCell setImagePosition:NSImageOnly];

            scratch = [[NSImage alloc] initWithSize:[self cellSize]];
            [scratch setFlipped:YES];
    }

    return self;
}

- (void)dealloc
{
    [leftCell release];
    [rightCell release];
    [scratch release];
    [labelColor release];

    [super dealloc];
}

#pragma mark Setters/Getters
- (void)setLeftButtonCell:(id)cell
{
    [leftCell release];
    leftCell = [cell retain];
}
- (id)leftButtonCell
{
    return leftCell;
}

- (void)setRightButtonCell:(id)cell
{
    [rightCell release];
    rightCell = [cell retain];
}
- (id)rightButtonCell
{
    return rightCell;
}

- (void)setLabelColor:(NSColor *)lColor
{
    [labelColor autorelease];
    labelColor = lColor;
    [labelColor retain];
}
- (NSColor *)labelColor
{
    return labelColor;
}

- (void)setAnimating:(BOOL)animating
{
    isAnimating = animating;
}

- (BOOL)isAnimating { return isAnimating; }

#pragma mark Sizing
- (NSRect)textRectForBounds:(NSRect)bounds
{
    NSRect rect = NSInsetRect(bounds, 2, 2);
        rect.size.height = [super drawingRectForBounds:bounds].size.height;

    { // Modify the bounds rect so our text gets drawn between our cells...
        NSSize leftSize = [leftCell cellSize];
        NSSize rightSize = [rightCell cellSize];
        
        rect.origin.x += leftSize.width;
        rect.size.width -= leftSize.width;
        
        rect.size.width -= rightSize.width;
    
        // Deal with our rounded variation...
        if ([self bezelStyle] == NSTextFieldRoundedBezel) {
            if (leftSize.width == 1) {
                rect.origin.x += 5;
                rect.size.width -= 5;
            }
            if (rightSize.width == 1)
                rect.size.width -= 5;
        }

    }

    return rect;
}

- (NSRect)leftButtonRectForBounds:(NSRect)bounds
{
    NSRect leftRect = NSMakeRect(0,0,0,0);
        leftRect.size = [leftCell cellSize];
    return leftRect;
}

- (NSRect)rightButtonRectForBounds:(NSRect)bounds
{
    NSRect rightRect = NSMakeRect(0,0,0,0);

    rightRect.size = [rightCell cellSize];
    rightRect.origin.x = bounds.size.width - rightRect.size.width;

    return rightRect;
}

- (NSRect)labelRectForBounds:(NSRect)bounds
{
    NSRect labelRect = [self textRectForBounds:bounds];
    if (![self isHighlighted]) { // guesses..
        labelRect.origin.x += 3;
        labelRect.origin.y += 2;
        labelRect.size.width -= 3;
    } else labelRect = [self rightButtonRectForBounds:bounds]; // mimic Finder?
    
    return labelRect;
}

- (NSRect)drawingRectForBounds:(NSRect)theRect
{
    return [self textRectForBounds:theRect];
}

#pragma mark Drawing
- (BOOL)showsFirstResponder { return NO; }
- (BOOL)drawsBackground { return NO; }

- (void)drawWithFrame:(NSRect)cellFrame inView:(NSView *)controlView
{
    NSImage *progress;
    NSRect pRect, lRect = [self labelRectForBounds:cellFrame];
    NSColor *lColor = [self labelColor];
    NSImage *superImage = [[NSImage alloc] initWithSize:cellFrame.size];
            [superImage setFlipped:YES];
            [scratch setSize:cellFrame.size];
            
            [superImage lockFocus]; // We want what would normally go in the controlView in an image instead.
            [super drawWithFrame:cellFrame inView:[NSView focusView]];
            [superImage unlockFocus];
 
    if ([controlView respondsToSelector:@selector(progressIndicator)]) { // grab the progress..
        NSProgressIndicator *indicator = [(CCDTextField *)controlView progressIndicator];
        pRect = NSOffsetRect([controlView bounds], 4, 4);
        progress = [[NSImage alloc] initWithSize:pRect.size];
        [progress setFlipped:YES];

            [progress lockFocus]; // draw the progress
            [indicator setFrameSize:pRect.size]; 
            [indicator drawRect:[indicator bounds]];   
            [progress unlockFocus];
    }

    [scratch lockFocus]; // composite..
        [[NSColor whiteColor] set];
        NSRectFill(cellFrame); // clear scratch of any previous drawing

     if (isAnimating) // progress..
        [progress drawAtPoint:NSZeroPoint fromRect:NSInsetRect(cellFrame, 2, 4) operation:NSCompositeSourceOver fraction:1];
    
    if (lColor) { // label..
        [lColor set];
        [NSBezierPath fillRoundRectInRect:lRect radius:90]; // no clue if this is right, wish it was a little rounder though.
    }
    
    [superImage drawAtPoint:NSZeroPoint fromRect:cellFrame operation:NSCompositeDestinationAtop fraction:1];
    [scratch unlockFocus];
    
    // draw it
    [scratch drawAtPoint:NSZeroPoint fromRect:cellFrame operation:NSCompositeSourceOver fraction:1];
    [super drawInteriorWithFrame:cellFrame inView:controlView];

    { // draw the left and right cells as needed
        [leftCell drawWithFrame:[self leftButtonRectForBounds:cellFrame] inView:controlView];
        [rightCell drawWithFrame:[self rightButtonRectForBounds:cellFrame] inView:controlView];
    }
}

#pragma mark Cursor
- (void)resetCursorRect:(NSRect)cellFrame inView:(NSView *)controlView
{
    [super resetCursorRect:[self textRectForBounds:cellFrame] inView:controlView];
}

#pragma mark Selection Handling
static id lastView;
 - (void)selectWithFrame:(NSRect)aRect inView:(NSView *)controlView editor:(NSText *)textObj delegate:(id)anObject start:(int)selStart length:(int)selLength
 {
     NSEvent *curEvent = [NSApp currentEvent];
     NSPoint mouseLoc= [controlView convertPoint:[curEvent locationInWindow] fromView:nil];
    
     if (![lastView isEqualTo:controlView]) {
        if ([curEvent type] == NSKeyDown)
            [super selectWithFrame:aRect inView:controlView editor:textObj delegate:anObject start:selStart length:selLength];
         else
             if (!NSPointInRect(mouseLoc, [self textRectForBounds:[controlView bounds]]))
                 [super selectWithFrame:aRect inView:controlView editor:textObj delegate:anObject start:0 length:self stringValue] length;
              else [super editWithFrame:aRect inView:controlView editor:textObj delegate:anObject event:curEvent];

            lastView = controlView;
            return;
     }

     if (!NSPointInRect(mouseLoc, [self textRectForBounds:[controlView bounds]])) {
         NSRange range = [(CCDPTextView*)textObj prvtSelectedRange];
         selStart = range.location;
         selLength = range.length;
         [super selectWithFrame:aRect inView:controlView editor:textObj delegate:anObject start:selStart length:selLength]; 
       return;
     }

    [super editWithFrame:aRect inView:controlView editor:textObj delegate:anObject event:curEvent];
}

@end


----
11/21; Added cursor handling and changed the default button highlighting.

11/25; Added the Selection Handling section.

11/29; Added the progress drawing.

12/2; Cleaned up some and added support for colored labels.

4/8; CCDImageCategory wasn't needed, as described on its page.

