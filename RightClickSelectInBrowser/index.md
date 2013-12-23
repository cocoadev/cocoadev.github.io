---
layout: page
title: RightClickSelectInBrowser
---



The "menu" outlet for NSBrowser doesn't seem to do very much, but in a similar way to the code for RightClickSelectInTableView you can make it work for NSBrowser cells (actually NSMatrix cells).

Firstly, subclass NSMatrix and call your subclass BrowserMatrixView. Include the following code (which could easily be improved upon, I would imagine):

    

@implementation BrowserMatrixView

-(NSBrowser* )browser {
	NSView* theView = [self superview];
	while(theView != nil) {
		if([theView isKindOfClass:[NSBrowser class]]) {
			return (NSBrowser* )theView;
		}
		theView = [theView superview];
	}	
	return nil;
}

-(int)matrixColumn {
	NSBrowser* theBrowser = [self browser];
	for(int i = [theBrowser firstVisibleColumn]; i <= [theBrowser lastVisibleColumn]; i++) {
		if([theBrowser matrixInColumn:i]==self) {
			return i;
		}
	}
	return -1;
}

-(NSMenu* )menuForEvent:(NSEvent* )event {
	NSPoint thePoint = [self convertPoint:[event locationInWindow] fromView:nil];
	int theRow = -1;
	int theCol = -1;
	if([self getRow:&theRow column:&theCol forPoint:thePoint] == NO) {
		return nil;
	}

	NSBrowser* theBrowser = [self browser];
	theCol = [self matrixColumn];
	if(theBrowser==nil || theCol==-1) {
		return nil;
	}
	if(theBrowser delegate] respondsToSelector:@selector(browser:selectRow:inColumn:)]) {
		[[NSLog(@"asking delegate to select row %d col %d",theRow,theCol);
		theBrowser delegate] browser:theBrowser selectRow:theRow inColumn:theCol];
	} else {
		[[NSLog(@"asking browser to select row %d col %d",theRow,theCol);
		[theBrowser selectRow:theRow inColumn:theCol];
	}

	// sets this matrix as the first responder for window
	self window] makeFirstResponder:self];
       // return the context menu for the browser
	return [[self browser] menu];
}
@end



Finally, in your controller (or [[NSBrowser subclass I suppose) you will need to tell the NSBrowser which class to use for matrix classes:

    

-(void)awakeFromNib {
	// set browser matrix class
	[myBrowser setMatrixClass:[BrowserMatrixView class]];
}



- DavidThorpe

