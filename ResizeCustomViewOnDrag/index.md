---
layout: page
title: ResizeCustomViewOnDrag
---



I'm trying to create a custom size grip like that found at the bottom of Mail 2.0. I already have the one-pixel split view bar, etc. and all is working well there. I found the following example for handling continuous dragging events in the Cocoa docs and adapted it to my purposes. The problem is that it works *too* well. In other words, if I drag anywhere in the view, the resizing code is executed.

The mouseDown: code is as follows:

    

- (void)mouseDown:(NSEvent *)event
{
    BOOL keepOn = YES;
	BOOL isInside = YES;
    NSPoint mouseLoc;
	NSRect rect;
    while (keepOn)
	{
        event = self window] nextEventMatchingMask:[[NSLeftMouseUpMask | NSLeftMouseDraggedMask];
        mouseLoc = [self convertPoint:[event locationInWindow] fromView:nil];
		isInside = [self mouse:mouseLoc inRect:GRIPRECT];
		
		switch ([event type]) {
			case NSLeftMouseDragged:
				rect = [self frame];
				rect.size.width += [event deltaX];
				[self setFrame:rect];
				self superview] display];
				break;
			case [[NSLeftMouseUp:
				keepOn = NO;
				break;
			default:
				break;
		}
    }
	return;
}



"GRIPRECT" is defined as follows:

    
#define GRIPRECT     NSMakeRect(NSWidth([self frame]) - 17, 0, 17, BOTTOM_BAR_HEIGHT)


If I modify the     case NSLeftMouseDragged: block to only execute if     isInside == YES, if you drag too fast, the resize doesn't keep up and the drag ends because it's no longer "    isInside". What's wrong with my assumptions? ;-)

----

Never mind ... ;-) I found it. I had to account for the initial mouseDown: event and react accordingly. The following seems to work.

    
- (void)mouseDown:(NSEvent *)event
{
    NSPoint mouseLoc = [self convertPoint:[event locationInWindow] fromView:nil];
	BOOL isInside = [self mouse:mouseLoc inRect:GRIPRECT];;
    BOOL keepOn = isInside;
	NSRect rect;
	
	if (!keepOn)
	{
		[super mouseDown:event]; //??
		return;
	}
	
    while (keepOn)
	{
        event = self window] nextEventMatchingMask:[[NSLeftMouseUpMask | NSLeftMouseDraggedMask];
        mouseLoc = [self convertPoint:[event locationInWindow] fromView:nil];
		isInside = [self mouse:mouseLoc inRect:GRIPRECT];
		
		switch ([event type]) {
			case NSLeftMouseDragged:
				rect = [self frame];
				rect.size.width += [event deltaX];
				[self setFrame:rect];
				self superview] display];
				break;
			case [[NSLeftMouseUp:
				keepOn = NO;
				break;
			default:
				[super mouseDown:event];
				break;
		}
    }
	return;
}


Note the line commented as "//??" above. Is this necessary? I'll have other controls added to this view as subviews. I thought that line might be necessary to allow normal operation for subviews. Also, is there anything else wrong above?

