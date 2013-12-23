---
layout: page
title: SearchTextFieldCell
---

----

I've left my starter code here, but would suggest interested programers checkout:

http://s.sudre.free.fr/Software/DevPotPourri.html

first. It implements the entire Mail.app search text field behavior.

- Jesse

----

Here's some starter code for creating a Mail.app like search field (rounded, magnifying glass, close button...). But it doesn't really work! I've seen a number of questions regarding this, hopefully we can get this fully functional, will probably save many people lots of time.

So here's my start, (I?ve come to the conclusion that I don?t really understand NSCell?s yet) I would encourage people to clean it up and make it as "cocoa" as possible, I'm looking for a working well done search field, I don't really care if it follows the design of my current partial solution. This code seems to draw mostly correctly, but I can?t seem to get mouse events and so can?t make the close button work. Also I haven?t added a popup menu for when the magnifying glass is clicked on, I don?t need that for my app, but it would still be nice to see how to do it.

I've provided a project builder project along with image resources for this code at: http://www.hogbay.com/software/SearchTextAreaExample.zip

    

//
//  SearchFieldCell.h
//
//  Created by Jesse Grosjean on Thu Nov 21 2002.
//

#import <Cocoa/Cocoa.h>


@interface SearchFieldCell : NSTextFieldCell {
    BOOL _showCancelButton;
}

- (NSRect)textRectForFrame:(NSRect)frame;

@end

//
//  SearchFieldCell.m
//
//  Created by Jesse Grosjean on Thu Nov 21 2002.
//

#import "SearchFieldCell.h"


@implementation SearchFieldCell

static NSImage *leftSearchCapImage = nil;
static NSImage *middleSearchImage = nil;
static NSImage *rightSearchCapImage = nil;
static NSImage *rightSearchStopCapImage = nil;
static NSImageCell *_leftCapCell;
static NSImageCell *_middleCell;
static NSImageCell *_rightCapCell;
static NSImageCell *_rightStopCapCell;
static float textOffset = 2.5; // yuck why do i use this?

+ (void)initialize {
    leftSearchCapImage = [[NSImage imageNamed: @"LeftSearchCap"] retain];
    middleSearchImage = [[NSImage imageNamed: @"MiddleSearch"] retain];
    rightSearchCapImage = [[NSImage imageNamed: @"RightSearchCap"] retain];
    rightSearchStopCapImage = [[NSImage imageNamed: @"RightSearchStopCap"] retain];
    _leftCapCell = [[NSImageCell alloc] initImageCell:leftSearchCapImage];
    _middleCell = [[NSImageCell alloc] initImageCell:middleSearchImage];
    _rightCapCell = [[NSImageCell alloc] initImageCell:rightSearchCapImage];
    _rightStopCapCell = [[NSImageCell alloc] initImageCell:rightSearchStopCapImage];
    [_leftCapCell setImageAlignment:NSImageAlignLeft];
    [_middleCell setImageScaling:NSScaleToFit];
    [_rightCapCell setImageAlignment:NSImageAlignRight];
    [_rightStopCapCell setImageAlignment:NSImageAlignRight];
}

- (id)initTextCell:(NSString *)text {
    if (self = [super initTextCell:text]) {
        _showCancelButton = [text length] > 0;
    }
    return self;
}

- (BOOL)showsFirstResponder {
    return NO;
}

- (BOOL)wraps {
    return NO;
}

- (BOOL)isScrollable {
    return YES;
}

- (NSText *)setUpFieldEditorAttributes:(NSText *)textObj {
    return [super setUpFieldEditorAttributes:textObj];
}

- (void)selectWithFrame:(NSRect)aRect 
                 inView:(NSView *)controlView 
                 editor:(NSText *)textObj 
               delegate:(id)anObject 
                  start:(int)selStart 
                 length:(int)selLength {

    [super selectWithFrame:[self textRectForFrame:aRect]
                    inView:controlView
                    editor:textObj
                  delegate:anObject
                     start:selStart
                    length:selLength];
}

- (void)drawInteriorWithFrame:(NSRect)cellFrame inView:(NSView *)controlView {
    NSRect textRect = [self textRectForFrame:cellFrame];
    NSRect middleRect = textRect;
    middleRect.origin.y -= textOffset;
    middleRect.origin.x -= 1;
    middleRect.size.width += 2;

    if ([super showsFirstResponder] && controlView window] isKeyWindow]) {
        [[[NSGraphicsContext saveGraphicsState];
        NSSetFocusRingStyle(NSFocusRingOnly);
        [_leftCapCell drawWithFrame:cellFrame inView:controlView];
        [_rightCapCell drawWithFrame:cellFrame inView:controlView];
        [_middleCell drawWithFrame:middleRect inView:controlView];
        [NSGraphicsContext restoreGraphicsState];
    }
    
    [_leftCapCell drawWithFrame:cellFrame inView:controlView];
    [_rightCapCell drawWithFrame:cellFrame inView:controlView];
    [_middleCell drawWithFrame:middleRect inView:controlView];
    
    if (_showCancelButton) {
        [_rightStopCapCell drawWithFrame:cellFrame inView:controlView];
    } else {
        [_rightCapCell drawWithFrame:cellFrame inView:controlView];
    }
    [super drawInteriorWithFrame:textRect inView:controlView];
}

- (void)editWithFrame:(NSRect)aRect 
               inView:(NSView *)controlView 
               editor:(NSText *)textObj 
             delegate:(id)anObject 
                event:(NSEvent *)theEvent {

    [super editWithFrame:[self textRectForFrame:aRect] 
                  inView:controlView 
                  editor:textObj 
                delegate:anObject 
                   event:theEvent];
}

- (NSRect)textRectForFrame:(NSRect)frame {
    frame.origin.x += [leftSearchCapImage size].width;
    frame.origin.y += textOffset;
    frame.size.width -= [leftSearchCapImage size].width;
    frame.size.width -= [rightSearchCapImage size].width; 
    return frame;
}

- (NSMenu *)menuForEvent:(NSEvent *)anEvent 
                  inRect:(NSRect)cellFrame ofView:(NSView *)aView {
    return [super menuForEvent:anEvent inRect:cellFrame ofView:aView];
}

- (void)resetCursorRect:(NSRect)cellFrame 
                 inView:(NSView *)controlView {

    [super resetCursorRect:[self textRectForFrame:cellFrame]
                    inView:controlView];
}

- (void)setStringValue:(NSString *)string {
    [super setStringValue:string];
    _showCancelButton = [string length] > 0;
}

// can't seem to get any mouse tracking to work.
- (BOOL)trackMouse:(NSEvent *)theEvent 
            inRect:(NSRect)cellFrame 
            ofView:(NSView *)controlView 
       untilMouseUp:(BOOL)untilMouseUp {

    NSLog(@"tracking");
    return [super trackMouse:theEvent 
                      inRect:cellFrame 
                      ofView:controlView 
                untilMouseUp:untilMouseUp];
}

- (BOOL)startTrackingAt:(NSPoint)startPoint inView:(NSView *)controlView {
    NSLog(@"start tracking");
    return [self startTrackingAt:startPoint inView:controlView];
//    return NO;
}

@end



----

You could probably subclass CCDTextField and/or CCDTextFieldCell and add some search-specific stuff pretty easily.

