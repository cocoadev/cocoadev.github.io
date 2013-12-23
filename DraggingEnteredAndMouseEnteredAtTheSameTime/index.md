---
layout: page
title: DraggingEnteredAndMouseEnteredAtTheSameTime
---

Hi there. I have discovered a annoying bug of NSTableView, but I'm not very sure if its really NSTableView<nowiki/>'s problem. I've written a little test app to reproduce this behaviour. It consists of a simple cocoa app with a single main window, with a custom view inside, that has a tracking rectangle that covers its entire bounds rectangle and accepts dragging of filenames. This is the code of the subview: 
    
/* MyView.h */

#import <Cocoa/Cocoa.h>

@interface MyView : NSView
{
	NSTrackingRectTag trackingRect;
}
@end

/* MyView.m */
#import "MyView.h"

@implementation MyView

- (id)initWithFrame:(NSRect)frameRect
{
	if ((self = [super initWithFrame:frameRect]) != nil) {
		// Add initialization code here
	}
	return self;
}

- (void)awakeFromNib
{
	[self registerForDraggedTypes:[NSArray arrayWithObject:NSFilenamesPboardType]];
	trackingRect = [self addTrackingRect:[self bounds] owner:self userData:nil assumeInside:NO];
}

- (void)drawRect:(NSRect)rect
{
}

- (void)mouseEntered:(id)sender
{
	NSLog(@"Mouse Entered");
}
- (void)mouseExited:(id)sender
{
	NSLog(@"Mouse Exited");
}

- (NSDragOperation)draggingEntered:(id <NSDraggingInfo>)sender
{
	NSLog(@"Dragging Entered");
	return NSDragOperationNone;
}
- (void)draggingExited:(id <NSDraggingInfo>)sender
{
	NSLog(@"Dragging Exited");
}

@end


As you can see, I'm just logging the mouseEntered, mouseExited, draggingEntered and draggingExited events. The bug I'm talking about is produced when you drag a file (or various files) from a column of NSTableView (or NSOutlineView), such as Finder's list view or column view, onto my custom view, but you have to start the drag from near the filename's end, not from the icon. When you do that, the view receives both mouseEntered and draggingExited messages when entered and both mouseExited and draggingExited messages when exited. If you drag from the icon just draggingEntered and draggingExited are received. Really annoying.

Is anyone else experiencing the same? Does someone has a workaround for this?

Thanks a lot.

Miro Keller

