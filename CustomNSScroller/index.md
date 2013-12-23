---
layout: page
title: CustomNSScroller
---

I am working on implementing a vertical CustomNSScroller for my app.  So far I have the following code:

    
// Draws the knob
- (void)drawKnob
{
	NSRect knobRect = [self rectForPart:NSScrollerKnob];
   
	NSRect knobRectTop = NSMakeRect(knobRect.origin.x, knobRect.origin.y, knobRect.size.width, 11);
	NSRect knobRectBottom = NSMakeRect(knobRect.origin.x, knobRect.origin.y+knobRect.size.height-10, knobRect.size.width, 10);
	NSRect knobRectFill = NSMakeRect(knobRect.origin.x, knobRect.origin.y+11, knobRect.size.width, knobRect.size.height-21);
	
	NSImage *knobImage;
	
	if (super window] isMainWindow] && [[super window] isKeyWindow]) {
		knobImage = [[[NSImage imageNamed:@"ScrollKnob"];
	} else {
		knobImage = [NSImage imageNamed:@"ScrollKnobInactive"];
	}
	
	[knobImage drawInRect:knobRectTop fromRect: NSMakeRect(0, 0, 15, 11) operation:NSCompositeSourceAtop fraction:1.0];
	[knobImage drawInRect:knobRectBottom fromRect: NSMakeRect(0, 21, 15, 10) operation:NSCompositeSourceAtop fraction:1.0];
	[knobImage drawInRect:knobRectFill fromRect: NSMakeRect(0, 11, 15, 10) operation:NSCompositeSourceAtop fraction:1.0];
}

// Draw the scroll bar arrows
- (void)drawArrow:(NSScrollerArrow)arrow highlightPart:(int)flag
{
	NSRect arrowRect = [self rectForPart:NSScrollerIncrementLine];
	arrowRect.origin.y -= 14;
	arrowRect.size.height = 30;
	NSImage *scrollArrows;
	
	if (super window] isMainWindow] && [[super window] isKeyWindow]) {
		scrollArrows = [[[NSImage imageNamed:@"ScrollArrowsDouble"];
	} else {
		scrollArrows = [NSImage imageNamed:@"ScrollArrowsDoubleInactive"];
	}
	
	[scrollArrows drawInRect:arrowRect fromRect: NSMakeRect(0, 14, 15, 30) operation:NSCompositeSourceOver fraction:1.0];
	
	if (arrow==NSScrollerDecrementArrow) {
		arrowRect = [self rectForPart:NSScrollerDecrementLine];
		arrowRect.origin.y -= 14;
		arrowRect.size.height += 14;
		
		if (flag==0) {
			[[NSImage imageNamed:@"ScrollArrowsDoubleActive"] drawInRect:arrowRect fromRect: NSMakeRect(0, 0, 15, 28) operation:NSCompositeSourceOver fraction:1.0];
		}
	}
	
	if (arrow==NSScrollerIncrementArrow) {
		arrowRect = [self rectForPart:NSScrollerIncrementLine];
		
		if (flag==0) {
			[[NSImage imageNamed:@"ScrollArrowsDoubleActive"] drawInRect:arrowRect fromRect: NSMakeRect(0, 28, 15, 16) operation:NSCompositeSourceOver fraction:1.0];
		}
	}
}

// Draws the knob background
- (void)drawKnobSlotInRect:(NSRect)rect highlight:(BOOL)highlight
{
	NSImage *scrollTrack;
	
	if (super window] isMainWindow] && [[super window] isKeyWindow]) {
		scrollTrack = [[[NSImage imageNamed:@"ScrollTrackFill"];
	} else {
		scrollTrack = [NSImage imageNamed:@"ScrollTrackFillInactive"];
	}
	
	[scrollTrack drawInRect:rect fromRect: NSMakeRect(0, 18, 15, 8) operation:NSCompositeSourceOver fraction:1.0];
	[scrollTrack drawInRect:NSMakeRect(rect.origin.x, rect.origin.y-4, rect.size.width, 18) fromRect: NSMakeRect(0, 0, 15, 18) operation:NSCompositeSourceOver fraction:1.0];
	[scrollTrack drawInRect:NSMakeRect(rect.origin.x, rect.origin.y+rect.size.height-18, rect.size.width, 18) fromRect: NSMakeRect(0, 26, 15, 18) operation:NSCompositeSourceOver fraction:1.0];
}


Only two problems are holding this back from having perfect functionality:

1.  When my window is not key, and the "inactive" version of the scroll bar is drawn, the whole scroll bar is not updated and only certain sections (around the arrows and knob) are updated to look "inactive".  Maybe I am approaching this wrong but I can't figure it out for the life of me.

2.  NSScrollerIncrementArrow gets the "highlight" flag perfectly and draws the "clicked" version of the arrow every time when it should.  However, NSScrollerDecrementArrow never gets the highlight flagged called at all even though I use the exact same code for both.  Any ideas?

Thanks for any help!

----
The flag variable of drawArrow:highlightPart: is -1 when nothing should be highlighted, 0 when the decrement button should be highlighted and 1 when the increment button should be highlighted. The only problem I have, is that the arrow variable doesn't pass the correct arrow (it's almost always NSScrollerIncrementArrow) to highlight. This is my solution (The only way I could make it work. If anybody has a better solution, please post it!).
    
- (void)drawArrow:(NSScrollerArrow)arrow highlightPart:(int)flag
{
	NSRect arrowRect;
	if (flag == -1) { // Don't highlight any button
		arrowRect = [self rectForPart:NSScrollerIncrementLine];
		[[NSImage imageNamed:@"incrementArrow"] drawInRect:arrowRect fromRect:NSZeroRect operation:NSCompositeSourceOver fraction:1.0];
		
		arrowRect = [self rectForPart:NSScrollerDecrementLine];
		[[NSImage imageNamed:@"decrementArrow"] drawInRect:arrowRect fromRect:NSZeroRect operation:NSCompositeSourceOver fraction:1.0];
	} else if (flag == NSScrollerIncrementArrow) { // Highlight increment button
		arrowRect = [self rectForPart:NSScrollerIncrementLine];
		[[NSImage imageNamed:@"incrementArrowHighlighted"] drawInRect:arrowRect fromRect:NSZeroRect operation:NSCompositeSourceOver fraction:1.0];
	} else if (flag == NSScrollerDecrementArrow) { // Highlight decrement button
		arrowRect = [self rectForPart:NSScrollerDecrementLine];
		[[NSImage imageNamed:@"decrementArrowHighlighted"] drawInRect:arrowRect fromRect:NSZeroRect operation:NSCompositeSourceOver fraction:1.0];
	}
}


----
Looks like it actually is drawing the decrementArrowHighlighted image, it's just that it immediately draws the decrementArrow right after it.  Not sure why, but I'll post again if I can figure it out.

----
In the past I've made drawArrow: and drawArrow empty methods and instead done all of the drawing in drawRect: .  I then used the NSScroller hitPart method to determine which piece to highlight  -- RbrtPntn

