---
layout: page
title: BorderedShadowedGrowingTextField
---

I have modified the code at FieldEditorIssues to produce a text field which has the appearance of the AddressBook / Library text fields mentioned in HighlightButtonsLikeDLandAB.

I'm completely at a loss as to how to go about turning one of these into a view with many editable text fields. Does anyone have any suggestions as to the best way to approach this? My preliminary thoughts are:

One view with many strings? Or many of these views in a custom superview? As to the first, how to manage the strings and click handling, for the second, how do we deal with multi-line resizing and moving the elements below? 

I'll post any improvements made back here as it seems to be a popular subject at the moment. -BB

Code follows:

    
@interface FieldEditorTestView : NSView
{
    NSRect itemRect;
    NSString *itemTitle;
    NSSize maxSize;
    NSTextView *mEditor;
	IBOutlet NSButton *editButton;
}
- (void)calculateMaxSize;
@end


    
#import "FieldEditorTestView.h"

@implementation FieldEditorTestView

- (BOOL)isFlipped
{
    return YES;
}

- (BOOL)isOpaque
{
    return NO;
}

- (void)setString:(NSString *)s
{
    [itemTitle release];
    itemTitle = [s retain];
    itemRect.size = [itemTitle sizeWithAttributes: nil];
	
	[self calculateMaxSize];
	
    if (itemRect.size.width > maxSize.width)
        itemRect.size.width = maxSize.width;
}

- (void)calculateMaxSize 
{
	maxSize = self superview] frame].size;
	maxSize.width -= itemRect.origin.x + 5;
	maxSize.height -= itemRect.origin.y + 5;
}

- (void)awakeFromNib
{ 
	itemRect.origin = [[NSMakePoint(5, 5);   
	[self calculateMaxSize];
	
    [self setString: @"Hello World"];
}

-(void)drawRect:(NSRect)r
{
	[[NSColor whiteColor] set];
    NSRectFill(r);
    
    if (mEditor == nil) {
        [[NSColor textBackgroundColor] set];
        NSRectFill(itemRect);
        
        [[NSColor textColor] set];
        NSMutableParagraphStyle *style = [[[NSMutableParagraphStyle alloc] init] autorelease];
        [style setLineBreakMode:NSLineBreakByTruncatingMiddle];
        NSMutableAttributedString *truncatedMiddle = [[NSMutableAttributedString alloc] initWithString:itemTitle];
        [truncatedMiddle addAttribute:NSParagraphStyleAttributeName 
								value:style 
								range:NSMakeRange(0, [itemTitle length])];
								
		if ([itemTitle isEqualToString:NSLocalizedString(@"no value", nil)]) {
			[truncatedMiddle addAttribute:NSForegroundColorAttributeName
									value:[NSColor grayColor]
									range:NSMakeRange(0, [itemTitle length])];
		}
        
        [truncatedMiddle drawInRect:itemRect];
		
    } else {
        NSResponder *resp = self window] firstResponder];
        if ([[self window] isKeyWindow] &&
            [resp isKindOfClass:[[[NSView class]] &&
            [(NSView *)resp isDescendantOf:self]) {
           
			[NSGraphicsContext saveGraphicsState];
           
			
				[self setFocusRingType:NSFocusRingTypeNone];
				
				NSRect outerRect = [mEditor frame];
				outerRect.size.width += 2;
				outerRect.size.height += 4;
				outerRect.origin.x -= 1;
				outerRect.origin.y -= 2;
				
				// Shadow for line
				NSShadow *shadow = [[NSShadow alloc] init];
				[shadow setShadowOffset:NSMakeSize(0, -2)];
				[shadow setShadowBlurRadius:4.0];
				[shadow setShadowColor:[NSColor colorWithDeviceWhite:0.5 alpha:0.3]];
				[shadow set];
				
				[[NSColor colorWithCalibratedRed:0.2226 green:0.46875 blue:0.83593 alpha:1.0] set];
				NSFrameRect(outerRect);

				// A sort of nil-shadow to avoid restoring the graphics state already
				[shadow setShadowOffset:NSZeroSize];
				[shadow setShadowBlurRadius:0];
				[shadow setShadowColor:[NSColor clearColor]];
				[shadow set];
				
				// Shrink outerRect to avoid drawing over line
				outerRect.size.width -= 2;
				outerRect.size.height -= 2;
				outerRect.origin.x += 1;
				outerRect.origin.y += 1;
				[[NSColor whiteColor] set];
				NSRectFill(outerRect);	
				
				[shadow release];
				shadow = nil;
            [NSGraphicsContext restoreGraphicsState];
        }
    }
}

- (void)fixEditor
{
    [mEditor setFrameSize:NSMakeSize(maxSize.width, 10000)];
    [mEditor sizeToFit];
		// Uncomment the following two lines to resize from centre
		//NSRect r = [mEditor frame];
		//[mEditor setFrameOrigin:NSMakePoint(NSMidX(itemRect) - r.size.width / 2, r.origin.y)];
    [self setNeedsDisplay:YES];
}

- (void)startEditing:(id)sender
{
	[self calculateMaxSize];
    mEditor = (NSTextView *)self window] fieldEditor:YES forObject:self];
    [mEditor setDelegate:self];
    [mEditor setHorizontallyResizable:YES];
    [mEditor setVerticallyResizable:YES];
    [mEditor setFrameSize:[[NSMakeSize(maxSize.width, 10000)];
    mEditor textContainer] setContainerSize:[[NSMakeSize(maxSize.width, 10000)];
    mEditor textContainer] setHeightTracksTextView:NO];
    [[mEditor textContainer] setWidthTracksTextView:NO];
    [mEditor setString:itemTitle];
    [mEditor selectAll:self];
    [mEditor setFrameOrigin:itemRect.origin];
    [self addSubview:mEditor];
    [[self window] makeFirstResponder:mEditor];
    [self fixEditor];
}

- (void)textDidChange:([[NSNotification *)notification;
{
    [self fixEditor];
}

- (void)textDidEndEditing:(NSNotification *)aNotification
{
    // you have to copy the string because it's mutable and gets reused!
	if ([aNotification object] string] isEqualToString:@""]) {
		[self setString:[[[NSString stringWithString:@"no value"]];
	} else {
		[self setString:[NSString stringWithString:aNotification object] string];
	}
    self window] makeFirstResponder:nil];
    [mEditor removeFromSuperview];
    mEditor = nil;
    [self setNeedsDisplay:YES];
}

- (void)mouseDown:([[NSEvent *)theEvent
{
    NSPoint p = [self convertPoint: [theEvent locationInWindow] fromView: nil];
    if (mEditor != nil) {
        self window] makeFirstResponder: nil];
    }
    if ([[NSPointInRect(p, itemRect)) {
        if ([theEvent clickCount] > 1) {
				[self startEditing:self];
        } else {
            NSSize offset = NSMakeSize(p.x - itemRect.origin.x,
                                       p.y - itemRect.origin.y);
            while (theEvent = self window] nextEventMatchingMask:
                [[NSLeftMouseDraggedMask|NSLeftMouseUpMask]) {
                if ([theEvent type] == NSLeftMouseDragged) {
                    NSPoint p = [self convertPoint: [theEvent locationInWindow]
                                          fromView: nil];
                    [self setNeedsDisplayInRect: itemRect];
                    itemRect.origin.x = p.x - offset.width;
                    itemRect.origin.y = p.y - offset.height;
                    [self setNeedsDisplayInRect: itemRect];
                    [self setNeedsDisplayInRect: NSMakeRect(0,0,100,20)];
                    
                }
                else break;
            }
        }
    }
}

@end

