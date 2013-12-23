---
layout: page
title: MailStyleGradientSelection
---

Hello,

I know this has been amply discussed elsewhere and there are various solutions, but thought this might be useful to somebody else out there. I wanted Mail 2.0/iTunes-style gradient selections in the NSOutlineView in my app. Thanks to others who have posted here and elsewhere, this wasn't too difficult. My implementation doesn't use an external image, and allows you to specify whether the gradient is contiguous across multiple selected rows, whether to have a one pixel gap between selected rows, and whether - like in Mail - the outline view (or table view) should only ever show the disabled/unfocused grey gradient colour and never turn blue even when you click on a row.

Note that you will need NSBezierPath_AMShading.h and NSBezierPath_AMShading.m (a category of NSBezierPath that makes it easy to do gradient fills) by Andreas Mayer from http://www.harmless.de/cocoa.html (these files are contained in the AMRolloverButton example code).

This code was particularly helped by the information given by one of the Omni guys here: http://wilshipley.com/blog/2005/07/pimp-my-code-part-3-gradient.html

Here is my subclass of NSOutlineView:

Interface file:

    

#import <Cocoa/Cocoa.h>

@interface KBGradientOutlineView : NSOutlineView
{
	NSIndexSet *draggedRows;
	
	BOOL usesGradientSelection;
	BOOL selectionGradientIsContiguous;
	BOOL usesDisabledGradientSelectionOnly;
	BOOL hasBreakBetweenGradientSelectedRows;
}

/* Useful for delegate when deciding how to colour text */
- (NSIndexSet *)draggedRows;

// Gradient selection methods
/* Sets whether the outline view should use gradient selection bars. */
- (void)setUsesGradientSelection:(BOOL)flag;
- (BOOL)usesGradientSelection;

/* Sets whether gradient selections should be contiguous across multiple
rows. (iTunes and Mail don't have this, but I think it looks better.) */
- (void)setSelectionGradientIsContiguous:(BOOL)flag;
- (BOOL)selectionGradientIsContiguous;

/* Sets whether the selection should always look disabled (grey), even
when the outline view has the focus (like in Mail) */
- (void)setUsesDisabledGradientSelectionOnly:(BOOL)flag;
- (BOOL)usesDisabledGradientSelectionOnly;

/* Sets whether selected rows have a pixel gap between them that is the
background colour rather than the selection colour */
- (void)setHasBreakBetweenGradientSelectedRows:(BOOL)flag;
- (BOOL)hasBreakBetweenGradientSelectedRows;
@end




Implementation file:

    

#import "KBGradientOutlineView.h"
#import "NSBezierPath_AMShading.h"	// For gradient highlighting


// We override (and may call) an undocumented private NSTableView method,
// so we need to declare that here
@interface NSObject (NSTableViewPrivateMethods)
- (id)_highlightColorForCell:(NSCell *)cell;
@end


@implementation KBGradientOutlineView

- (void)viewWillMoveToWindow:(NSWindow *)newWindow
{
	NSNotificationCenter *nc = [NSNotificationCenter defaultCenter];
	
	[nc removeObserver:self name:NSWindowDidResignKeyNotification object:nil];
	[nc addObserver:self
		   selector:@selector(windowDidChangeKeyNotification:)
			   name:NSWindowDidResignKeyNotification
			 object:newWindow];
	
	[nc removeObserver:self name:NSWindowDidBecomeKeyNotification object:nil];
	[nc addObserver:self
		   selector:@selector(windowDidChangeKeyNotification:)
			   name:NSWindowDidBecomeKeyNotification
			 object:newWindow];
}

- (void)dealloc
{
	[[NSNotificationCenter defaultCenter] removeObserver:self];
	[super dealloc];
}

- (void)windowDidChangeKeyNotification:(NSNotification *)notification
{
	[self setNeedsDisplay:YES];
}

- (void)highlightSelectionInClipRect:(NSRect)clipRect
{
	if (!usesGradientSelection)
	{
		[super highlightSelectionInClipRect:clipRect];
		return;
	}
	
	NSColor *topLineColor, *bottomLineColor, *gradientStartColor, *gradientEndColor;
	
	// Color will depend on whether or not we are the first responder
	NSResponder *firstResponder = self window] firstResponder];
	if ( (![firstResponder isKindOfClass:[[[NSView class]]) ||
		 (![(NSView *)firstResponder isDescendantOf:self]) ||
		 (!self window] isKeyWindow]) ||
		 ([self usesDisabledGradientSelectionOnly]) )
	{
		topLineColor = [[[NSColor colorWithDeviceRed:(173.0/255.0) green:(187.0/255.0) blue:(209.0/255.0) alpha:1.0];
		bottomLineColor = [NSColor colorWithDeviceRed:(150.0/255.0) green:(161.0/255.0) blue:(183.0/255.0) alpha:1.0];
		gradientStartColor = [NSColor colorWithDeviceRed:(168.0/255.0) green:(183.0/255.0) blue:(205.0/255.0) alpha:1.0];
		gradientEndColor = [NSColor colorWithDeviceRed:(157.0/255.0) green:(174.0/255.0) blue:(199.0/255.0) alpha:1.0];
	}
	else
	{
		topLineColor = [NSColor colorWithCalibratedRed:(61.0/255.0) green:(123.0/255.0) blue:(218.0/255.0) alpha:1.0];
		bottomLineColor = [NSColor colorWithCalibratedRed:(31.0/255.0) green:(92.0/255.0) blue:(207.0/255.0) alpha:1.0];
		gradientStartColor = [NSColor colorWithCalibratedRed:(89.0/255.0) green:(153.0/255.0) blue:(209.0/255.0) alpha:1.0];
		gradientEndColor = [NSColor colorWithCalibratedRed:(33.0/255.0) green:(94.0/255.0) blue:(208.0/255.0) alpha:1.0];
	}
	
	NSIndexSet *selRows = [self selectedRowIndexes];
	int rowIndex = [selRows firstIndex];
	int endOfCurrentRunRowIndex, newRowIndex;
	NSRect highlightRect;
	
	while (rowIndex != NSNotFound)
	{
		if ([self selectionGradientIsContiguous])
		{
			newRowIndex = rowIndex;
			do {
				endOfCurrentRunRowIndex = newRowIndex;
				newRowIndex = [selRows indexGreaterThanIndex:endOfCurrentRunRowIndex];
			} while (newRowIndex == endOfCurrentRunRowIndex + 1);
			
			highlightRect = NSUnionRect([self rectOfRow:rowIndex],[self rectOfRow:endOfCurrentRunRowIndex]);
		}
		else
		{
			newRowIndex = [selRows indexGreaterThanIndex:rowIndex];
			highlightRect = [self rectOfRow:rowIndex];
		}
		
		if ([self hasBreakBetweenGradientSelectedRows])
			highlightRect.size.height -= 1.0;
		
		[topLineColor set];
		NSRectFill(highlightRect);
		
		highlightRect.origin.y += 1.0;
		highlightRect.size.height-=1.0;
		[bottomLineColor set];
		NSRectFill(highlightRect);
		
		highlightRect.size.height -= 1.0;
			
		[[NSBezierPath bezierPathWithRect:highlightRect] linearGradientFillWithStartColor:gradientStartColor
																				 endColor:gradientEndColor];
		
		rowIndex = newRowIndex;
	}
}

- (id)_highlightColorForCell:(NSCell *)cell
{
	if (!usesGradientSelection)
		return [super _highlightColorForCell:cell];
	return nil;
}

- (void)selectRow:(int)row byExtendingSelection:(BOOL)extend
{
	[super selectRow:row byExtendingSelection:extend];
	
	// If we are using a contiguous gradient, we need to force a redraw of more than
	// just the current row - all selected rows will need redrawing
	if ([self usesGradientSelection]&&[self selectionGradientIsContiguous])
		[self setNeedsDisplay:YES];
}

- (void)selectRowIndexes:(NSIndexSet *)rowIndexes byExtendingSelection:(BOOL)extend
{
	[super selectRowIndexes:rowIndexes byExtendingSelection:extend];
	
	// If we are using a contiguous gradient, we need to force a redraw of more than
	// just the current row - all selected rows will need redrawing
	if ([self usesGradientSelection]&&[self selectionGradientIsContiguous])
		[self setNeedsDisplay:YES];
} 

- (void)deselectRow:(int)row;
{
	[super deselectRow:row];
	
	// If we are using a contiguous gradient, we need to force a redraw of more than
	// just the current row in case multiple are selected, as selected rows will need redrawing
	if ([self usesGradientSelection]&&[self selectionGradientIsContiguous])
		[self setNeedsDisplay:YES];
}

- (NSImage *)dragImageForRowsWithIndexes:(NSIndexSet *)dragRows tableColumns:(NSArray *)tableColumns 
								   event:(NSEvent*)dragEvent offset:(NSPointPointer)dragImageOffset
{
	// We need to save the dragged row indexes so that the delegate can choose how to colour the
	// text depending on whether it is being used for a drag image or not (eg. selected row may
	// have white text, but we still want to colour it black when drawing the drag image)
	draggedRows = dragRows;
	
	NSImage *image = [super dragImageForRowsWithIndexes:dragRows tableColumns:tableColumns
										event:dragEvent offset:dragImageOffset];
	
	draggedRows = nil;
	return image;
}

- (NSIndexSet *)draggedRows
{
	return draggedRows;
}

- (void)setUsesGradientSelection:(BOOL)flag
{
	usesGradientSelection = flag;
	[self setNeedsDisplay:YES];
}

- (BOOL)usesGradientSelection
{
	return usesGradientSelection;
}

- (void)setSelectionGradientIsContiguous:(BOOL)flag
{
	selectionGradientIsContiguous = flag;
	[self setNeedsDisplay:YES];
}

- (BOOL)selectionGradientIsContiguous
{
	return selectionGradientIsContiguous;
}

- (void)setUsesDisabledGradientSelectionOnly:(BOOL)flag
{
	usesDisabledGradientSelectionOnly = flag;
	[self setNeedsDisplay:YES];
}

- (BOOL)usesDisabledGradientSelectionOnly
{
	return usesDisabledGradientSelectionOnly;
}

- (void)setHasBreakBetweenGradientSelectedRows:(BOOL)flag
{
	hasBreakBetweenGradientSelectedRows = flag;
	[self setNeedsDisplay:YES];
}

- (BOOL)hasBreakBetweenGradientSelectedRows
{
	return hasBreakBetweenGradientSelectedRows;
}

@end



A side effect of overriding these methods is that the text will remain black even when selected, which is annoying (I guess this is because drawRow:clipRect: checks that the selection colour is blue to determine whether or not to use white text). This can be fixed in the outline view's delegate, as follows:

    
- (void)outlineView:(NSOutlineView *)olv willDisplayCell:(id)cell forTableColumn:(NSTableColumn *)tableColumn item:(id)item
{	
	// If the row is selected but isn't being edited and the current drawing isn't being used to create a drag image,
	// colour the text white; otherwise, colour it black
	int rowIndex = [olv rowForItem:item];
	NSColor *fontColor = ( olv selectedRowIndexes] containsIndex:rowIndex] && ([olv editedRow] != rowIndex) && (![[([[KBOutlineView *)olv draggedRows] containsIndex:rowIndex]) ) ?
		[NSColor whiteColor] : [NSColor blackColor];
	[cell setTextColor:fontColor];
}


Finally, if you also want a nice blue background like in Mail or iTunes, the color is:

[NSColor colorWithDeviceRed:(231.0/255.0) green:(237.0/255.0) blue:(248.0/255.0) alpha:1.0]

Some grabs:

http://www.rumramruf.com/ScrivenerBeta/ScrivenerGradient1.jpg

http://www.rumramruf.com/ScrivenerBeta/ScrivenerGradient2.jpg

http://www.rumramruf.com/ScrivenerBeta/ScrivenerGradient3.jpg

http://www.rumramruf.com/ScrivenerBeta/ScrivenerGradient4.jpg

http://www.rumramruf.com/ScrivenerBeta/ScrivenerGradient5.jpg

Hope that is useful to someone,
KB

----
Very cool!

Another (simple, featureless) solution that just came to me..
    
- (id)_highlightColorForCell:(NSCell *)cell { return [NSColor colorWithPatternImage:theGradientImage]; }


Not nearly as cool or flexible but it seemed to work well enough in my tests.

Thanks... I just realised that my original code didn't take into account drag and drop, so that if you dragged a selected row, its text would be drawn in white in the drag image. I've updated it to account for that.
KB

----

I added a couple optimization-oriented comments. :)

----

Thanks. The reason those parts call [self setNeedsDisplay:YES] rather than [self setNeedsDisplayInRect:[self rectOfRow:row]] is that they need to force display of more than just the row being selected when the gradient is contiguous - all selected rows have to be redrawn... I've added comments to the code to make that clearer. I suppose it could iterate through the selected rows and make a union rect, but with the extra iteration I doubt that would optimise much. :) I have optimised it very slightly, though, so that it only redraws everything if there is a multiple selection (EDIT: and changed it back again, because that caused some minor drawing glitches when going from multiple selection to single). KB

----
I should've known that already. *smacking forehead* Thanks for the clarification.

----

Updated 15/12/05: Just got rid of the NSIntersectionRect(highlightRect,clipRect) stuff, as this causes drawing problems when scrolling because the gradient can get drawn in the wrong place. It means drawing more than strictly necessary, but that's not too heavy. KB

----

Instead of using a delegate, you can do the following (it doesn't break bindings; it just alters the cell before display):

    

- (void) drawRow: (int) rowIndex clipRect: (NSRect) clipRect
{
	NSColor *theColor;

	if (self window] firstResponder] == self && [[self selectedRowIndexes] containsIndex:rowIndex] && ([self editedRow] != rowIndex))
		theColor = [[[NSColor whiteColor];
	else
		theColor = [NSColor blackColor];
				
	NSEnumerator *enu = self tableColumns] objectEnumerator];
	[[NSTableColumn *col;
	
	while (col = [enu nextObject])
		if ([[col dataCell] respondsToSelector:@selector(setTextColor:)])
			[(id)[col dataCell] setTextColor:theColor];

	[super drawRow:rowIndex clipRect:clipRect];
}


