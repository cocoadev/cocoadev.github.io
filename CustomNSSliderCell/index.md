---
layout: page
title: CustomNSSliderCell
---

I've created a custom NSSlider and it looks wonderful. The problem is that when I set an action and target either in the interface builder or by code it doesnt work. The slider will not perform the action. Please help! Here is my code:

    
@implementation progressSliderCell

- (id)init 
{
	self = [super init];
    if (self) {
		progressLeftImage = [[NSImage imageNamed:@"LCDSliderLeft"] retain];
		progressRightImage = [[NSImage imageNamed:@"LCDSliderRight"] retain];
		progressCenterImage = [[NSImage imageNamed:@"LCDSliderCenter"] retain];
		
		progressKnobImage = [[NSImage imageNamed:@"Scrub"] retain];
    }
    return self;
}

- (void) dealloc
{
	[progressLeftImage release];
	[progressRightImage release];
	[progressCenterImage release];
	[progressKnobImage release];
	
	[super dealloc];
}

- (id)copyWithZone:(NSZone *)zone {
    progressSliderCell *newCopy = progressSliderCell alloc] init];
    return newCopy;
}

- (void)sendActionToTarget {
    if ([self target] && [self action]) {
        [([[NSControl *)[self controlView] sendAction:[self action] to:[self target]];
    }
}

- (float)knobThickness
{
	return 11;
}

- (void)drawKnob:(NSRect)knobRect
{	
	// Create a Canvas
	NSImage *canvas = [[[NSImage alloc] initWithSize:knobRect.size] autorelease];
	NSRect canvasRect = NSMakeRect(0, 0, [canvas size].width, [canvas size].height);
	
	// Draw fill onto Canvas
	[canvas lockFocus];
	[progressKnobImage setSize:knobRect.size];
	[progressKnobImage compositeToPoint:NSMakePoint(0,2)
							  operation:NSCompositeSourceOver];
	[canvas unlockFocus];
	
	self controlView] lockFocus];
	[canvas drawInRect:knobRect
			  fromRect:canvasRect
			 operation:[[NSCompositeSourceOver
			  fraction:1.0];
	self controlView] unlockFocus];
}

- (void)drawBarInside:([[NSRect)aRect flipped:(BOOL)flipped
{
	// Create a Canvas
	NSImage *canvas = [[[NSImage alloc] initWithSize:aRect.size] autorelease];
	NSRect canvasRect = NSMakeRect(0, 0, [canvas size].width, [canvas size].height);
	
	// Draw fill onto Canvas
	[canvas lockFocus];
	[progressLeftImage compositeToPoint:NSMakePoint(0, 2) 
							 operation:NSCompositeSourceOver];
	
	[progressRightImage compositeToPoint:NSMakePoint([canvas size].width - [progressRightImage size].width, 2) 
							   operation:NSCompositeSourceOver];
	
	NSSize middleSize = NSMakeSize([canvas size].width - [progressLeftImage size].width - [progressRightImage size].width, [progressCenterImage size].height);
	[progressCenterImage setScalesWhenResized:YES];
	[progressCenterImage setSize:middleSize];
	[progressCenterImage compositeToPoint:NSMakePoint([progressLeftImage size].width, 2) 
								operation:NSCompositeSourceOver];
	[canvas unlockFocus];
		
	// Draw canvas
	self controlView] lockFocus];
	[canvas drawInRect:aRect
			  fromRect:canvasRect
			 operation:[[NSCompositeSourceOver
			  fraction:1.0];
	self controlView] unlockFocus];
}

- (void) drawInteriorWithFrame:([[NSRect)cellFrame inView:(NSView*)controlView {
    cellFrame = [self drawingRectForBounds:cellFrame];
    [controlView lockFocus];
    //_trackRect = cellFrame;
    [self drawBarInside:cellFrame flipped:[controlView isFlipped]];
    [self drawKnob];
    [controlView unlockFocus];
}

@end

@implementation progressSlider

+ (void)initialize {
    if (self == [progressSlider class]) {
        [self setCellClass:[progressSliderCell class]];
    }
}

+ (Class)cellClass {
    return [progressSliderCell class];
}

- (id)init
{
	self = [super init];
	if (self) {
		progressSliderCell *aCell = [progressSliderCell alloc] init] autorelease];;
		[aCell setControlSize:[[NSSmallControlSize];
		[aCell setSliderType:NSLinearSlider];
		
		[self setCell:aCell];
	}
	return self;
}

- (void)dealloc
{
	[super dealloc];
}

- (void)performClick:(id)sender {
    self cell] performClick:sender];
}

@end


----

You should probably implement your     copyWithZone: method to actually create a copy. The way you have it, it creates a new object, but copies no attributes over.

Since you don't have any extra attributes to copy, you can probably just call through to     super, or just remove your override altogether and let [[NSSliderCell take care of making copies.

*There's probably no need to implement     performClick: either.*

**Nor     sendAction:toTarget:.  Nor dealloc on the NSSlider subclass.  If you aren't changing the parent's behavior, you don't have to implement the method.**

----

I tried to use this subclass in one of my projects, and it doesn't work. I put the interface declarations in, and it doesn't give any errors, but the slider doesn't work, either. What am I doing wrong? I put in the declarations in a .h file, and declared the nsimage instances, and then added it with the nsslider to the IB file (and applied it to the slider). What am I missing?
----
Because of the way IB creates objects, you have to use a custom view and change its class to progressSlider (lowercase class names are about as far from the standard as you can get, by the way). If you change an NSSlider's class, it will make it a progressSlider -- but the slider *cell* will still be an NSSliderCell. Alternatively, you could add code like this (modified from one of my own projects) to change the necessary NSSliderCell<nowiki/>s to progressSliderCells. --JediKnil
    
// Within the view class...

- (id)initWithCoder:(NSCoder *)decoder
{
	BOOL shouldUseSetClass = NO;
	BOOL shouldUseDecodeClassName = NO;
	if ([decoder respondsToSelector:@selector(setClass:forClassName:)]) {
		shouldUseSetClass = YES;
		[(NSKeyedUnarchiver *)decoder setClass:[progressSliderCell class] forClassName:@"NSSliderCell"];
		
	} else if ([decoder respondsToSelector:@selector(decodeClassName:asClassName:)]) {
		shouldUseDecodeClassName = YES;
		[(NSUnarchiver *)decoder decodeClassName:@"NSSliderCell" asClassName:@"progressSliderCell"];
	}

	self = [super initWithCoder:decoder];

	if (shouldUseSetClass) {
		[(NSKeyedUnarchiver *)decoder setClass:[NSSliderCell class] forClassName:@"NSSliderCell"];
	} else if (shouldUseDecodeClassName) {
		[(NSUnarchiver *)decoder decodeClassName:@"NSSliderCell" asClassName:@"NSSliderCell"];
	}
	
	return self;
}


----
JediKnil's approach is a good and sound trick. However, it shouldn't hard-code the restoring of the class. Instead, before calling NSKeyedUnarchiver's -setClass:forClassName: it should first call -classForClassName: and store the returned Class. Then when restoring, call -setClass:forClassName: passing the saved-off Class as the 1st argument.  In the NSUnarchiver case, call -classNameDecodedForArchiveClassName: to save off the NSString* of the class name, then pass that back to -decodeClassName:asClassName: when restoring.  So, good approach, just don't assume what you're restoring is what you wanted to replace... save off the old value, set it to the new, then restore the old value.

