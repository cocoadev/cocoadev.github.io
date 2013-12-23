---
layout: page
title: NSTableViewBezeledBorder
---



How can I create table with a border like the one in iTunes. Although iTunes is carbon application, however, there must be some way to do it with cocoa as (off the top of my head) NewsFire and some other apps achieve the same effect. 

Anybody done this before? 

- JohnDevor

----

There are various AppKit drawing functions like     NSDrawWhiteBezel() that might work.

----

    NSDrawWhiteBezel() does not produce the same effect as in iTunes. However, I managed to produce the correct border by simply drawing the border myself.

- JohnDevor
----

I created an NSView subclass that does this...
    

#import "XBorderedView.h"

static NSColor * _topColor = nil;
static NSColor * _bottomColor = nil;
static NSColor * _sideColor = nil;
static NSColor * _interiorColor = nil;

@implementation XBorderedView

+ (void)initialize {
	if(self == [XBorderedView class]) {
		_bottomColor = [[NSColor colorWithCalibratedWhite:0.55 alpha:1.0] retain];
		_topColor = [[NSColor colorWithCalibratedWhite:0.94 alpha:1.0] retain];
		_sideColor = [[NSColor colorWithCalibratedWhite:0.87 alpha:1.0] retain];
		_interiorColor = [[NSColor colorWithCalibratedWhite:0.38 alpha:1.0] retain];
	}
}

- (id)initWithFrame:(NSRect)frame {
    self = [super initWithFrame:frame];
    if (self) {

    }
    return self;
}

- (void)awakeFromNib {
	[self packSubviews];
}

- (void)addSubview:(NSView*)subview {
	[super addSubview:subview];
	[self packSubviews];
}

- (void)packSubviews {
	NSArray * subViews = [self subviews];
	NSView * subView = [subViews lastObject];
	
	NSRect fullFrame = [self bounds];
	
	fullFrame.origin.x += 2.0;
	fullFrame.origin.y += 2.0;
	fullFrame.size.height -= 3.0;
	fullFrame.size.width -= 4.0;
	
	[subView setFrame:fullFrame];
}

- (void)drawRect:(NSRect)rect {
	NSRect lineRect = rect;
	
	lineRect.origin.x += 1.0;
	lineRect.size.width -= 2.0;
	lineRect.size.height = 1.0;
	
	[_topColor set];
	NSRectFill(lineRect);
	
	lineRect.origin.y += (rect.size.height - 1.0);
	
	[_bottomColor set];
	NSRectFill(lineRect);
	
	lineRect = rect;
	lineRect.origin.y += 1.0;
	lineRect.size.height -= 2.0;
	lineRect.size.width = 1.0;
	
	[_sideColor set];
	NSRectFill(lineRect);
	
	lineRect.origin.x += (rect.size.width - 1.0);
	NSRectFill(lineRect);
	
	NSRect insetRect = NSInsetRect(rect, 1.0, 1.0);
	[_interiorColor set];
	NSFrameRect(insetRect);
	
	[self packSubviews];
}

- (BOOL)mouseDownCanMoveWindow {
	return YES;
}


@end



-Julian

----

It's not kosher to delete other people's posts because you think them unnecessary.

*Agreed. Pruning digressions and non-sequiturs, however, is acceptable practice. The rest should stay. I feel the discussion regarding the GPL issue is valid and relevant because some do question what rights they have to write their *own* code that does exactly the same common thing, such as the metal bezel effect on this page. I vote we keep it here for others.*

----

The colors seemed off (to my untrained eye, YMMV) so I used the set below but this and CCDGradientSelectionTableView gets you pretty darn close to a "table with a border like the one in iTunes".

    
                _bottomColor = [[[NSColor whiteColor] shadowWithLevel:0.3] retain];
		_topColor = [[[NSColor whiteColor] shadowWithLevel:0.1] retain];
                _sideColor = [[[NSColor whiteColor] shadowWithLevel:0.1] retain];
                _interiorColor = [[NSColor grayColor] retain];


----

One has already been done by another project out there: http://cvs.sourceforge.net/viewcvs.py/cocoalicious/cocoalicious/UI/SFHFBezelView.m?view=markup

