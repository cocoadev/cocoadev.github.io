---
layout: page
title: IphotoAdjustmentWindow
---

Hello!

I was wondering if anyone knew how to make that neat iPhoto 5 Adjustment window?
Looking at the nibs it seems that there are several custom views that are imported into a borderless window.

Any hints and tricks are most welcome

----

Yes it is a borderless window with custom controls.

----

Thanks, anyone that has some example code?

----

Check out BorderlessWindow and NSBezierPathCategory - these two together will give you everything you need. Just create a borderless window using custom view. In that custom view, draw a rounded rect (with NSBezierPath - the category provided in NSBezierPathCategory will help you here) with an alpha faded black color as the bezier path's fill color). Make some controls and add them to the custom view and viola!


----

THANKS!!

----

I recently found myself implementing some of the HUD interface for the preview window in Pixen. Along the way, I made a custom view that behaves just like the button in the HUD there. A screenshot:

http://www.opensword.org/Images/hudbuttons.jpg

PXHUDButton.h:

    
//
//  PXHUDButton.h
//  Pixen-XCode
//
//  Created by Andy Matuschak on 5/7/05.
//  Copyright 2005 Open Sword Group. All rights reserved.
//

#import <Cocoa/Cocoa.h>


@interface PXHUDButton : NSView
{
	NSString * title;
	NSImage * buttonFillUp, * buttonFillDown;
	NSImage * buttonLeftUp, * buttonLeftDown;
	NSImage * buttonRightUp, * buttonRightDown;
	int pressed;
	
	id target;
	SEL action;
}

- (void)setTitle:(NSString *)title;
- (void)setTarget:target;
- (void)setAction:(SEL)action;

@end


PXHUDButton.m:

    
//
//  PXHUDButton.m
//  Pixen-XCode
//
//  Created by Andy Matuschak on 5/7/05.
//  Copyright 2005 Open Sword Group. All rights reserved.
//

#import "PXHUDButton.h"


@implementation PXHUDButton

- (id)initWithFrame:(NSRect)frame {
    self = [super initWithFrame:frame];
    if (self)
	{
		buttonFillUp = [[NSImage imageNamed:@"hudButtonFillUp"] retain];
		buttonFillDown = [[NSImage imageNamed:@"hudButtonFillDown"] retain];
		buttonLeftUp = [[NSImage imageNamed:@"hudButtonLeftUp"] retain];
		buttonLeftDown = [[NSImage imageNamed:@"hudButtonLeftDown"] retain];
		buttonRightUp = [[NSImage imageNamed:@"hudButtonRightUp"] retain];
		buttonRightDown = [[NSImage imageNamed:@"hudButtonRightDown"] retain];
    }
    return self;
}

- (void)setTitle:(NSString *)newTitle
{
	[title release];
	title = [newTitle retain];
}

- (void)dealloc
{
	[buttonFillUp release];
	[buttonFillDown release];
	[buttonLeftUp release];
	[buttonLeftDown release];
	[buttonRightUp release];
	[buttonRightDown release];
	[title release];
	[super dealloc];
}

- (void)setButtonState:(int)state
{
	pressed = state;
	[self setNeedsDisplay:YES];
}

- (void)mouseDown:(NSEvent *)event
{
	[self setButtonState:NSOnState];
}

- (void)mouseUp:(NSEvent *)event
{
	[self setButtonState:NSOffState];
	if (target)
	{
		[target performSelector:action withObject:self];
	}
}

- (void)mouseDragged:(NSEvent *)event
{
	NSPoint point = [event locationInWindow];
	if (!NSPointInRect(point, [self frame]) && pressed == YES)
	{
		[self setButtonState:NSOffState];
	}
	else if (NSPointInRect(point, [self frame]) && pressed == NO)
	{
		[self setButtonState:NSOnState];
	}
}

- (void)setTarget:aTarget
{
	target = aTarget;
}

- (void)setAction:anAction
{
	action = anAction;
}

- (void)drawRect:(NSRect)rect
{
	// draw left side
	[(pressed ? buttonLeftDown : buttonLeftUp) compositeToPoint:NSZeroPoint operation:NSCompositeSourceAtop];
	
	// draw right side
	NSPoint right;
	right.x = NSMaxX([self bounds]) - [buttonRightUp size].width;
	right.y = 0;
	[(pressed ? buttonRightDown : buttonRightUp) compositeToPoint:right operation:NSCompositeSourceAtop];
	
	// draw middle
	NSRect middle;
	middle.origin.x = [buttonLeftUp size].width;
	middle.origin.y = 0;
	middle.size.width = [self frame].size.width - [buttonLeftUp size].width - [buttonRightUp size].width;
	middle.size.height = [buttonFillUp size].height;
	[(pressed ? buttonFillDown : buttonFillUp) drawInRect:middle fromRect:NSMakeRect(0,0,1,[buttonFillUp size].height) operation:NSCompositeSourceAtop fraction:1];
	
	NSString *actualString = [NSString stringWithFormat:@"<span style=\"font-size: 10px; font-family: 'Lucida Grande'; color: #ffffff;\"><strong>%@</strong></span>",title];
	NSAttributedString * actual = [[NSAttributedString alloc] initWithHTML:[actualString dataUsingEncoding:NSASCIIStringEncoding] documentAttributes:NULL];
	NSPoint stringPoint;
	stringPoint.x = [self frame].size.width / 2 - [actual size].width / 2;
	stringPoint.y = 4;
	[actual drawAtPoint:stringPoint];
}

@end


You'll need the images for it (ganked from iPhoto ^_^;;). You can download them (hudButton*.tiff) from our SVN repository here: http://www.andymatuschak.org/websvn/listing.php?repname=Open%20Sword%20SVN&path=/Pixen/trunk/&rev=0&sc=1

Just do PXHUDButton *myButton = [[PXHUDButton alloc] initWithFrame:someFrame] and make it a subview of something. The height has to be 20, I'm afraid. Use setTitle: to set the caption that appears inside the button, and setTarget: and setAction: to set up what happens when you click it. If someone feels like making an IBPalette, that'd be kinda sweet. I'm not entirely sure how.

I was thinking of making a whole suite of these things. Like, an overridden NSWindow, too, and so on. iPhoto has images for these widgets: spinner, slider, and button. It seems like Motion has many, many more, but I can't find the images for them in any of the frameworks included. Other things like text boxes can be drawn with bezier paths. Oh, and the window that contains this button should have 66% alpha if you're trying to replicate the HUD look. Here's the code I use to draw the main window view:

    
id path = [NSBezierPath bezierPathWithRoundedRect:NSInsetRect(frame, 1, 1) cornerRadius:8];
[[NSColor blackColor] set];
[path fill];


Pretty simple. It uses the RoundedRectangles category.

Oh, and I couldn't figure out how to make the tiny drop shadow draw under the button. It just didn't want to render no matter what I did. Anyone have any advice on that?

-- AndyMatuschak

----

Andy,

It appears that the images you're rendering are being drawn upside-down. If you look closely, you'll notice that the gradient is flipped, and the shadow is on top. Just to make sure I wasn't seeing things (no pun intended), I flipped the images vertically in Photoshop and sure enough, they drew as expected.

Unfortunately, I can't figure out *why* they're being drawn upside-down. There doesn't seem to be anything explicity wrong with your drawing code.

The non-destructive workaround would be to set the isFlipped attribute of each image to YES with -setFlipped and then adjust the vertical positioning of the title string with:

    
// minus one extra pixel for the shadow at the bottom
stringPoint.y = [self frame].size.height / 2 - [actual size].height / 2 - 1;


-- Michael Watson

----

Hmm... I can't seem to make the close button draw correctly. I'm using the same close button image that apple includes with iPhoto 06, but mine draws lighter. I've set it as the image of a button I put in the upper left corner of the window, which sits atop a view that draws the titlebar and rounded rect background. My background and titlebar seem to be the same color as iPhoto's, but the image is lighter... Any ideas? -- Devin Lane

