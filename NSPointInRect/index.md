---
layout: page
title: NSPointInRect
---

Tests whether the specified point is in the rectangle.

Example:
    
- (void)mouseMoved:(NSEvent *)theEvent
{
	NSPoint mouseLoc = [self convertPoint:[theEvent locationInWindow] toView:nil];
	if (NSPointInRect(mouseLoc, [self rectForBall]))
		// *mouse on*
	else
		// *mouse off*
}

