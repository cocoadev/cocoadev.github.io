---
layout: page
title: NSTableViewBackgroundColor
---



I was looking for examples of how to set an NSTableView row's background color based on a value in the row. I didn't find anything but came up with a method I thought I'd share. I subclassed NSTableView and overrode drawBackgroundInClipRect:. It gets the background color from the delegate through a call to tableView:backgroundColorForRow:. To leave the background color at the default, the delegate simply returns nil.

Here's the header (including a protocol that the delegate can conform to):


    
@interface RowColorTableView : NSTableView {
	BOOL delegateRespondsToBackgroundColorForRow;
}

@end

@protocol RowColorTableViewDelegate <NSObject>
- (NSColor *)tableView:(RowColorTableView *)aTableView backgroundColorForRow:(NSInteger)rowIndex;
@end


and the implementation:

    
#import "RowColorTableView.h"

@implementation RowColorTableView

- (void)awakeFromNib {
	delegateRespondsToBackgroundColorForRow = self delegate] respondsToSelector:@selector(tableView:backgroundColorForRow:)];
}

- (void)drawBackgroundInClipRect:([[NSRect)clipRect {
	//First draw default background
	[super drawBackgroundInClipRect:clipRect];
	
	if (delegateRespondsToBackgroundColorForRow) {
		//Now draw custom background where necessary
		NSRange rowRange = [self rowsInRect:clipRect];
		for (int rowIndex=rowRange.location; rowIndex<rowRange.location + rowRange.length; rowIndex++) {
			NSColor *bgndColor = self delegate] tableView:self backgroundColorForRow:rowIndex];
			if (bgndColor) {
				[bgndColor set];
				[[[NSBezierPath fillRect:[self rectOfRow:rowIndex]];
			}
		}
	}
}

@end


-SteveNicholson

