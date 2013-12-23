---
layout: page
title: HowToCodeForHoverButtonState
---



Does anyone know how to code so that when I hover the mouse over a button the "image" changes?

I am using a custom face for the button and I have all 4 states (normal, down, disabled and hover)
But in IB I can ony control the first two.

----
Well, IB's still not going to help you much, but you can subclass NSButton and override     mouseEntered:,     mouseExited:, and     setEnabled:. In each of those methods you can change the image. (Your alternate/down image can probably stay the same the whole time, since it'll only happen after a hover anyway). That should be enough to get you started...but if you need more help just ask. --JediKnil

----

I recently implemented a similar thing in the form of a Hyperlink type button.  One tip from someone who knows (I want to save others the trouble of banging their head against the wall for a day...)...make sure you hit test the mouse at the moment the event arrives at the     mouseEntered: method of your control, dont just blindly do the rollover.  You may find that if a user moves it in and out at a fast enough rate that the event queue gets bogged down with events that ar no longer meaningful and you end up with your control highlighting and unhighligting for a while after.  Once you do this you should get the effect you are after.

JKP

----

Seems you need to use AddTrackingRect to handle tracking rects before you can even receive     mouseEntered: and     mouseExited: messages. This is very tricky to get right. I posted some code that might help on the AddTrackingRect page.

--WAHa

----

My implementation:
    

#import "HoverButtonCell.h"

@interface NSButtonCell()
- (void)_updateMouseTracking;
@end

@implementation HoverButtonCell
@synthesize hoverImage;

- (void)mouseEntered:(NSEvent *)event {
	if (hoverImage != nil && [hoverImage isValid]) {
		_oldImage = ([[NSButton *)[self controlView] image] retain];
		[(NSButton *)[self controlView] setImage:hoverImage];
	}
}

- (void)mouseExited:(NSEvent *)event {
	if (_oldImage != nil && [_oldImage isValid]) {
		[(NSButton *)[self controlView] setImage:_oldImage];
		[_oldImage release];
		_oldImage = nil;
	}
}

- (void)_updateMouseTracking {
	[super _updateMouseTracking];
	if ([self controlView] != nil && self controlView] respondsToSelector:@selector(_setMouseTrackingForCell:)]) {
		[[self controlView] performSelector:@selector(_setMouseTrackingForCell:) withObject:self];
	}
}

- (void)setHoverImage:([[NSImage *)newImage {
	[newImage retain];
	[hoverImage release];
	hoverImage = newImage;
	self controlView] setNeedsDisplay:YES];
}

- (void)dealloc {
	[_oldImage release];
	[hoverImage release];
	[super dealloc];
}

@end


Then the .h file:

    
#import <Cocoa/Cocoa.h>

@interface [[HoverButtonCell : NSButtonCell {
	NSImage *_oldImage;
	NSImage *hoverImage;
}

@property (retain) NSImage *hoverImage;

@end


It's a little hacky, but I hope this helps.

--NP

