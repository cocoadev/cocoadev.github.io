---
layout: page
title: CCDGradientSelectionTableView
---

This is just a prettied up version of what's in NSTableViewRollover. (Moved from CCDSourceTable.)

Never mind the inaccurate description, see the example: http://homepage.mac.com/ryanstevens/SourceList.zip
    
// Copyright (c) 2005 Ryan Stevens.
//
// Permission is hereby granted, free of charge, to any person obtaining a
// copy of this software and associated documentation files (the "Software"),
// to deal in the Software without restriction, including without limitation
// the rights to use, copy, modify, merge, publish, distribute, sublicense,
// and/or sell copies of the Software.
//
// THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
// IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
// FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL
// THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
// LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
// FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER
// DEALINGS IN THE SOFTWARE.
//

#import <Cocoa/Cocoa.h>

@interface CCDGradientSelectionTableCell : NSTextFieldCell {} @end

@interface CCDGradientHeaderView : NSTableHeaderView
{
	NSImage *headerGradient;
	NSString *headerTitle;
}
- (void)setTitle:(NSString *)title;
- (NSString *)title;
@end

@interface CCDGradientSelectionTableView : NSTableView
{
	NSImage *blueGradient;
	NSImage *grayGradient;
	NSRect iRect;

		int mouseOverRow;
		int lastOverRow;
		NSTrackingRectTag trackingTag;
                BOOL mouseInView;
}

- (int)mouseOverRow;
@end


    
#import "CCDGradientSelectionTableView.h"


// You'll want to do something like this in the class you use as your dataCell.
@implementation CCDGradientSelectionTableCell

- (void)drawWithFrame:(NSRect)frame inView:(NSView *)controlView
{
	if ([controlView isKindOfClass:[CCDGradientSelectionTableView class]]) {
		frame.origin.y += 4;
		frame.origin.x -= 4;
	}
	[super drawWithFrame:frame inView:controlView];
}

// This keeps the fieldEditor in check. 
- (void)selectWithFrame:(NSRect)aRect inView:(NSView *)controlView editor:(NSText *)textObj delegate:(id)anObject start:(int)selStart length:(int)selLength
{
	if ([controlView isKindOfClass:[CCDGradientSelectionTableView class]]) {
		aRect.size = [self cellSize];
		aRect.origin.y += 4;
		aRect.origin.x -= 4;
		
	[controlView performSelector:@selector(prvtResizeTheFieldEditor) withObject:nil afterDelay:0.001];
	}
	[super selectWithFrame:aRect inView:controlView editor:textObj delegate:anObject start:selStart length:selLength];
}

@end


@implementation CCDGradientHeaderView

static NSString *headerImageData = @"<4d4d002a 00000074 fe6afe97 fed2fee8 00e6ffe5 00e4ffe3 fee1fede 02dcdbdc 02d9d8d9 02d6d5d6 02d2d1d2 02cfcecf fecbfec8 fec4fec0 febdfeb9 feb5feb2 00afffae 00abffaa 00a8ffa7 fea402a1 a0a1029e 9d9e029b 9a9b0298 97980295 9495fe92 fe90008e ff8d008c ff8b007c ff7bfe6b 000c0100 00030000 00010001 00000101 00030000 00010024 00000102 00030000 00030000 010a0103 00030000 00018005 00000106 00030000 00010002 00000111 00040000 00010000 00080115 00030000 00010003 00000117 00040000 00010000 006c011a 00050000 00010000 0110011b 00050000 00010000 0118011c 00030000 00010001 00000128 00030000 00010002 00000000 00000008 00080008 000afc80 00002710 000afc80 00002710 >";

- (id)initWithFrame:(NSRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
		headerGradient = [[NSImage alloc] initWithData:[headerImageData propertyList]];
			[headerGradient setFlipped:YES];
			[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(windowChangedKey:) name:NSWindowDidResignKeyNotification object:nil];
			[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(windowChangedKey:) name:NSWindowDidBecomeKeyNotification object:nil];
			headerTitle = nil;
    }
    return self;
}

- (void)dealloc
{
	[[NSNotificationCenter defaultCenter] removeObserver:self];
	[headerGradient release];
	[headerTitle release];
	[super dealloc];
}

- (void)setTitle:(NSString *)title
{
	[headerTitle autorelease];
	headerTitle = [title retain];
}
- (NSString *)title
{
	return headerTitle;
}

- (void)drawRect:(NSRect)rect
{
	[headerGradient drawInRect:rect fromRect:NSMakeRect(0,0,1,[headerGradient size].height) operation:NSCompositeSourceOver fraction:1];
	
	if (headerTitle) {
		float cornerAdjustment = NSMidX([self tableView] cornerView] frame]);
		[[NSDictionary *attributes = [NSDictionary dictionaryWithObjectsAndKeys:
					[NSFont fontWithName:@"Lucida Grande" size:11], NSFontAttributeName,
					[[NSColor lightGrayColor] highlightWithLevel:0.3], NSForegroundColorAttributeName,
					nil, nil];
		NSRect textRect;
			textRect.size = [headerTitle sizeWithAttributes:attributes];
		NSPoint point = NSMakePoint(cornerAdjustment+NSMidX(rect)-NSMidX(textRect), 2.5);

			[headerTitle drawAtPoint:point withAttributes:attributes];
	
				point.y = 1;
		
			if (self window] isKeyWindow]) {
				attributes = [[[NSDictionary dictionaryWithObjectsAndKeys:
					[NSFont fontWithName:@"Lucida Grande" size:11], NSFontAttributeName,
					[NSColor textColor], NSForegroundColorAttributeName,
					nil, nil];
				[headerTitle drawAtPoint:point withAttributes:attributes];
			} else {
				attributes = [NSDictionary dictionaryWithObjectsAndKeys:
					[NSFont fontWithName:@"Lucida Grande" size:11], NSFontAttributeName,
					[NSColor disabledControlTextColor], NSForegroundColorAttributeName,
					nil, nil];
				[headerTitle drawAtPoint:point withAttributes:attributes];
			}
	}
}

- (void)windowChangedKey:(id)note
{
	if (self tableView] editedRow] != -1)
		[[self window] makeFirstResponder:[self tableView; // ends editing

	[self setNeedsDisplay:YES];
}

-(void)mouseDown:(NSEvent *)event { self window] makeFirstResponder:[self tableView; }
@end


@implementation CCDGradientSelectionTableView

static NSString *blueImageData = @"<4d4d002a 00000048 800f4f6d a2ca65ca 564b390a 69371941 1ee22622 dc04743b 7c86826e 900fcdb1 d9e5b237 3ab60647 06b0b8d8 d5151a1a 82732348 46616888 4bcd00f9 719f0100 000d0100 00030000 00010001 00000101 00030000 00010012 00000102 00030000 00030000 00ea0103 00030000 00010005 00000106 00030000 00010002 00000111 00040000 00010000 00080115 00030000 00010003 00000116 00040000 00010000 2aaa0117 00040000 00010000 003f011a 00050000 00010000 00f0011b 00050000 00010000 00f8011c 00030000 00010001 00000128 00030000 00010002 00000000 00000008 00080008 000afc80 00002710 000afc80 00002710 >";
static NSString *grayImageData = @"<4d4d002a 0000006c 808080e5 7e7e7ee5 7d7d7de5 7c7c7ce5 7a7a7be5 787778e5 777676e5 747474e5 737373e5 727171e5 706f6fe5 6d6d6ce5 6c6c6be5 6a6a6ae5 696969e5 676767e5 656565e5 636464e5 616161e5 616161e5 606161e5 5f5e5fe5 5e5e5ee5 5d5d5de5 5c5c5ce5 000d0100 00030000 00010001 00000101 00030000 00010019 00000102 00030000 00040000 010e0103 00030000 00010001 00000106 00030000 00010002 00000111 00040000 00010000 00080115 00030000 00010004 00000117 00040000 00010000 0064011a 00050000 00010000 0116011b 00050000 00010000 011e011c 00030000 00010001 00000128 00030000 00010002 00000152 00030000 00010001 00000000 00000008 00080008 0008000a fc800000 2710000a fc800000 2710>";

// You'll want to remove this if you use a custom dataCell
+ (void)initialize { [CCDGradientSelectionTableCell poseAsClass:[NSTextFieldCell class]]; }

- (void)awakeFromNib
{
		self window] setAcceptsMouseMovedEvents:YES];
		
		blueGradient = [[[[NSImage alloc] initWithData:[blueImageData propertyList]];
		grayGradient = [[NSImage alloc] initWithData:[grayImageData propertyList]];
		iRect = NSMakeRect(0,0,1,[blueGradient size].height);
		
                mouseInView = YES;
		mouseOverRow = -1;
		lastOverRow = -1;


		[self setRowHeight:25];
		[[self tableColumns] objectAtIndex:0] dataCell] setFocusRingType:[[NSFocusRingTypeNone]; //!

		[self viewDidEndLiveResize];

			{ // Replace the header and corner view...
			NSRect rect = [self frame];
				rect.size.height = 17;
			id header = [[CCDGradientHeaderView alloc] initWithFrame:rect];
			id corner = [[CCDGradientHeaderView alloc] initWithFrame:NSMakeRect(0,0,15,17)];
                                [header setTitle:@"Source"];
				[self setHeaderView:header];
				[self setCornerView:corner];
				[header release];
				[corner release];
			}
}

- (void)viewWillStartLiveResize
{
	[super viewWillStartLiveResize];

	// end editing, as iTunes does...
	if ([self editedRow] != -1) self window] makeFirstResponder:self];
}
- (void)viewDidEndLiveResize
{
	[super viewDidEndLiveResize];
	[self removeTrackingRect:trackingTag];
	trackingTag = [self addTrackingRect:[self frame] owner:self userData:nil assumeInside:NO];
}

- (void)dealloc
{
	[blueGradient release];
	[grayGradient release];
	[self removeTrackingRect:trackingTag];
	[super dealloc];
}

- (int)mouseOverRow { return mouseOverRow; }
- (void)mouseEntered:([[NSEvent*)theEvent { mouseInView = YES; }

- (void)mouseMoved:(NSEvent*)theEvent
{
	if (mouseInView) {
		mouseOverRow = [self rowAtPoint:[self convertPoint:[theEvent locationInWindow] fromView:nil]];
			if (lastOverRow == mouseOverRow) return;
			else {
				[self setNeedsDisplayInRect:[self rectOfRow:lastOverRow]];
				[self setNeedsDisplayInRect:[self rectOfRow:mouseOverRow]];
				lastOverRow = mouseOverRow;
			}
		}
}

- (void)mouseExited:(NSEvent *)theEvent
{
		[self setNeedsDisplayInRect:[self rectOfRow:lastOverRow]];
                mouseInView = NO;
		mouseOverRow = -1;
		lastOverRow = -1;
}

- (BOOL)resignFirstResponder
{
                mouseInView = NO;
		mouseOverRow = -1;
		lastOverRow = -1;

	return [super resignFirstResponder];
}

- (id)_highlightColorForCell:(NSCell *)cell { return nil; }
- (void)highlightSelectionInClipRect:(NSRect)rect
{
	int selectedRow = [self selectedRow];
	
			[blueGradient setFlipped:YES];
			[blueGradient drawInRect:[self rectOfRow:selectedRow] fromRect:iRect operation:NSCompositeSourceOver fraction:1];
			[blueGradient setFlipped:NO];

		if ([self editedRow] == -1)
		if (self window] firstResponder] != self || ![[self window] isKeyWindow])
			[grayGradient drawInRect:[self rectOfRow:selectedRow] fromRect:iRect operation:[[NSCompositeSourceOver fraction:0.9];

		if (mouseInView) 
			[blueGradient drawInRect:[self rectOfRow:mouseOverRow] fromRect:iRect operation:NSCompositeSourceOver fraction:0.4];

	if ([self editedRow] != -1)
		[self performSelector:@selector(prvtResizeTheFieldEditor) withObject:nil afterDelay:0.001];
}

- (void)textDidChange:(NSNotification *)aNotification
{
		[super textDidChange:aNotification];
		[self prvtResizeTheFieldEditor];
}

- (void)prvtResizeTheFieldEditor
{
	id editor = self window] fieldEditor:YES forObject:self];
	[[NSRect editRect = NSIntersectionRect([self rectOfColumn:[self editedColumn]],[self rectOfRow:[self editedRow]]);
	NSRect frame = editor superview] frame];
	[[NSLayoutManager *layoutManager = [editor layoutManager];

        [layoutManager boundingRectForGlyphRange:NSMakeRange(0, [layoutManager numberOfGlyphs]) inTextContainer:[editor textContainer]];
        frame.size.width = [layoutManager usedRectForTextContainer:[editor textContainer]].size.width;

		if (editRect.size.width > frame.size.width) editor superview] setFrame:frame];
		else {
			frame.size.width = editRect.size.width-4;
			[[editor superview] setFrame:frame];
		}

	[editor setNeedsDisplay:YES];

		if ([self lockFocusIfCanDraw]) {
		id clipView = [[[self window] fieldEditor:YES forObject:self] superview];
		[[NSRect borderRect = [clipView frame];

			// get rid of the white border, leftover from resizing the fieldEditor..
					editRect.origin.x -= 6;
                                        editRect.size.width += 6;
			[blueGradient setFlipped:YES];
			[blueGradient drawInRect:editRect fromRect:iRect operation:NSCompositeSourceOver fraction:1];
			[blueGradient setFlipped:NO];

			// fix up the borderRect..
			borderRect.origin.y -= 1;
			borderRect.origin.x -= 1;
			borderRect.size.height += 2;
			borderRect.size.width += 2;
			
		// draw the border...
		[[NSColor blackColor] set];
		NSFrameRect(borderRect);
		
	[self unlockFocus];
	}
}@end


Your delegate should look something like this...
    
- (void)tableView:(NSTableView *)aTableView willDisplayCell:(id)aCell forTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex
 {
	if ([aCell type] == NSTextCellType && [aCell respondsToSelector:@selector(setTextColor:)]) {
		
		if ([aTableView editedRow] == rowIndex) [aCell setTextColor:[NSColor blackColor]];
		else
		if ([aTableView selectedRow] == rowIndex) [aCell setTextColor:[NSColor whiteColor]];
		else
			[aCell setTextColor:[NSColor blackColor]];

	}
 }


----

Without a custom fieldEditor editing isn't going to work exactly like it does in iTunes but I think I've got it pretty close here.

Fixed a glitch. My bad.

----**Discussion**----

Looks great! However theres a pretty decent bug at least on Mac OS X 10.4.2 where when you load the Sample app with the iTunes like table and everything looks good, then you switch to another window or app and take the focus away from the app with the table and the Header title disappears, when you bring focus back to the app it works just like before except the title is gone. Anybody else get this? I've tried this in my app and in the sample app and it's reproducable 100% of the time for me so far.

----

I, too, am getting that bug, but for my uses, I didn't really like the whole textured header thing anyway, so I just used the regular Aqua header. I never really attempted to chase down the cause. I suspect that if you were to use needsDisplay: on it, when it becomes firstResponder, then that would force it to redraw, and get around the issue, but it may not help figure out the original cause. Someone, please correct me if I'm wrong. -- JasonTerhorst

----
Sounds like the header view isn't getting its notifications. *shrug*

