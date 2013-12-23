---
layout: page
title: NSMatrixForTiling
---



**TileView.h**
    
#import <Cocoa/Cocoa.h>

@interface TileView : NSMatrix {
	NSSize minSize;
	NSImage *image;
}
- (void)setMinSize:(NSSize)size;
@end

@interface Tile : NSActionCell 
@end


**TileView.m**
    
#import "TileView.h"

static unsigned TileCount(float dim, float min, float gap, float *cellDim) {
	unsigned cnt = (unsigned)(dim / (min + gap));
	if (!cnt) cnt++;
	*cellDim = (dim / (float)cnt) - gap;
	return cnt;
}

@implementation TileView // NSMatrix

- (void)retile {
	NSSize sz = [self frame].size;
	NSSize newCellSize, spacing = [self intercellSpacing];
	[self renewRows:TileCount(sz.height, minSize.height, spacing.height, &newCellSize.height)
			columns:TileCount(sz.width, minSize.width, spacing.width, &newCellSize.width)];
	[self setCellSize:newCellSize];
	if (image) self cells] makeObjectsPerformSelector:@selector(setImage:) withObject:image];
	[self setNeedsDisplay:YES];
}
- (void)setFrame:([[NSRect)frame {
	[super setFrame:frame];
	[self retile];
}
- (void)setMinSize:(NSSize)size {minSize = size;}
- (void)setImage:(NSImage *)img {
	[img retain];
	[image release];
	[(image = img) setFlipped:YES];
}

- (void)dealloc {
	[image release];
	[super dealloc];
}

- (void)awakeFromNib {
	[self setCellClass:[Tile class]];
	[self setMinSize:NSMakeSize(128.0f, 128.0f)];
	[self setBackgroundColor:[NSColor blackColor]];
	[self setDrawsBackground:YES];
	[self setIntercellSpacing:NSMakeSize(32.0f, 32.0f)];
	[self setImage:[[[NSImage alloc] initWithContentsOfFile:@"/tmp/test.jpg"] autorelease]];	
	[self retile];	

}

- (void)drawRect:(NSRect)rect {

	// asking super to drawRect here will tile the background with the image
	[super drawRect:rect];	
	
	// your custom drawing code here	
	[[NSColor colorWithCalibratedRed:0.0f green:1.0f blue:0.0f alpha:0.5f] set];
	NSRect greenRect = NSInsetRect(rect, NSWidth(rect) / 4.0f, NSHeight(rect) / 4.0f);
	NSRectFillListUsingOperation(&greenRect, 1, NSCompositeSourceOver);
}

- (NSRect)boundsForFrame:(NSRect)fr size:(NSSize)size {
	NSSize spacing = [self intercellSpacing];
	float srcRatio = size.width / size.height;
	NSRect bounds = (srcRatio > NSWidth(fr) / NSHeight(fr)) 
						? NSInsetRect(fr, 0.0f, (NSHeight(fr) - NSWidth(fr) / srcRatio) / 2.0f)
						: NSInsetRect(fr, (NSWidth(fr) - NSHeight(fr) * srcRatio) / 2.0f, 0.0f);
	return NSOffsetRect(bounds, spacing.width / 2.0f, spacing.height / 2.0f);
}

@end

@implementation Tile
- (void)drawWithFrame:(NSRect)frame inView:(NSView *)view {
	NSImage *img = [self image];
	NSRect srcRect = NSZeroRect;
	srcRect.size = [img size];
	self image] drawInRect:[([[TileView *)view boundsForFrame:frame size:srcRect.size]
					fromRect:srcRect
					operation:NSCompositeSourceOver 
					fraction:1.0f];	
}
  
@end


--zootbobbalu

----

I haven't tried it, but wouldn't this be a lot easier by using     + (NSColor *)colorWithPatternImage:(NSImage*)image;
 and then just filling the whole view, forgetting about NSMatrix?

----

Hey that's pretty cool. Never noticed that method before. You can draw different images for each cell in the matrix, but     colorWithPatternImage is really nice because you can fill shapes. I also noticed that the pattern will start at the bottom left corner of the window, so if you want to have the tile conform to a view you can't use     colorWithPatternImage. --zootbobbalu

----

Interesting point about starting in the corner of the window, I guess it wouldn't work too well for this view, then.

You can constrain any arbitrary drawing to fill a shape by doing [shape addClip] before drawing, assuming shape is an NSBezierPath. This will clip all future drawing to be inside the given path, which comes in handy sometimes.

