---
layout: page
title: NSWindowCategory
---



NSWindow+CCDWindowTinting.h

    
#import <Cocoa/Cocoa.h>

@interface NSWindow (CCDWindowTinting)
- (NSColor *)baseWindowBackgroundColor;
- (void)removeTint;
- (void)tintWithColor:(NSColor *)color;
@end



NSWindow+CCDWindowTinting.m

    
#import "NSWindow+CCDWindowTinting.h"

NSColor *baseWindowBackgroundColor;

@implementation NSWindow (CCDWindowTinting)

- (NSColor *)baseWindowBackgroundColor
{
	if (!baseWindowBackgroundColor) baseWindowBackgroundColor = self backgroundColor] retain];
	
	return baseWindowBackgroundColor;
}

- (void)removeTint
{
   [self setBackgroundColor:nil];
   [self performSelector:@selector(display) withObject:nil afterDelay:0];
}

- (void)tintWithColor:([[NSColor *)color
{
	NSRect winRect = [self frame];
	NSImage *img = [[NSImage alloc] initWithSize:winRect.size];

              winRect.origin = NSZeroPoint;

		[img lockFocus];		
		[color set];
		NSRectFillUsingOperation(winRect, NSCompositeSourceOver);

		self baseWindowBackgroundColor] set];
		[[NSRectFillUsingOperation(winRect, NSCompositePlusDarker);
		[img unlockFocus];

	[self setBackgroundColor:[NSColor colorWithPatternImage:img]];
	[self performSelector:@selector(display) withObject:nil afterDelay:0];

	[img release];
}

@end


----
A quick addition for window tinting. The backgroundColor will tile if the window size changes but you can overcome that if you need to. Enjoy.

-- RyanStevens

