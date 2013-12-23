---
layout: page
title: NSWindowAndDataWithPDFInsideRect
---



I have an NSWindow with a custom view, InnerView as its contentView. Here's the code for InnerView

    
- (void) drawRect:(NSRect)rect {
	rect = [self frame];
	[[NSColor whiteColor] set];
	NSRect clearRect = rect;
	clearRect.origin.y += margin;
	clearRect.size.height -= margin;
	NSRectFill(clearRect);
	
	if (winCapture==nil || (NSEqualSizes([winCapture size],[SolWin frame].size)==NO)) {
		if (inCapture++ == 0) {
			NSTimer *timer = [NSTimer timerWithTimeInterval:0.1f target:self selector:@selector(captureWin:) userInfo:nil repeats:NO];
			[[NSRunLoop currentRunLoop] addTimer:timer forMode:NSDefaultRunLoopMode];
		}
	} else {
		NSRect wrect = [SolWin frame];
		wrect.origin = NSZeroPoint;
		[winCapture drawInRect:clearRect fromRect:wrect operation:NSCompositeSourceOver fraction:1.0];
	}
}
- (void)captureWin:(NSTimer*)timer {
	NSRect wrect = [SolWin frame];
	wrect.origin = NSZeroPoint;
	NSData *data = [SolWin dataWithPDFInsideRect:wrect];
	if (winCapture!=nil)
		[winCapture release];
	winCapture = [[NSImage alloc] initWithData:data];
	[self setNeedsDisplay:YES];
	inCapture = 0;
}


The result looks like this:
http://lavacat.com/wiki.gif

Why do I get black corners? -- gekko513
----

What you are seeing is an RGBA image that isn't drawing its alpha component correctly. If you save a tiff of the image and view the tiff in "Preview" you will see the corners drawn correctly. --zootbobbalu

    
- (void)drawRect:(NSRect)rect {
	[[NSColor redColor] set]; NSRectFill(rect);
}

- (void)captureWindow {
	NSLog(@"<%p>%s:", self, __PRETTY_FUNCTION__);
	NSRect windowFrame = self window] frame];
	windowFrame.origin = [[NSZeroPoint;
	NSData *data = self window] dataWithPDFInsideRect:windowFrame];
	[[NSImage *image = [[NSImage alloc] initWithData:data];
	NSData *tiff = [image TIFFRepresentation];
	[tiff writeToFile:@"/tmp/window.tiff" atomically:YES];
}

- (void)mouseDown:(NSEvent *)theEvent {
	[self captureWindow];
}


----

You'll need to override -isOpaque to return NO if you want to draw anything with alpha or transparency.
----
Strange. None of your suggestions seem to work. I even copied the example code from --zoot directly and I still got black corners, also on /tmp/window.tiff. I tried to override isOpaque, for all additional 3 combinations of adding the method to my NSWindow subclass S<nowiki/>olWindow and the InnerView. I finally tried to remove the [self setOpaque:NO] that I had called in S<nowiki/>olWindow.

Thanks, by the way, for trying to help out, and so quickly, too. -- gekko513

(And if I swap dataWithPDFInsideRect with dataWithEPSInsideRect, I get 3 corners almost correct but the bottom left corner has an even larger black square.)

