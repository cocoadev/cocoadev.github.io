---
layout: page
title: HowDoIRenderAnNSTextViewIntoAnImage
---

I've been using a technique to draw an NSTextView in an NSPanel, grab an image snapshot, and then close the NSPanel. The problem with that is, 1) even when the panel's rect is set for -10000 on both its x and y positions, the user can still see parts of the window occasionally, and 2) there are bugs in the implementation that cause parts of the window to appear in the image when the view is scaled above a certain size.
In addition, I wanted transparency (so I would only get the text in the image, no background), and merely creating the panel and text view programmatically didn't work with transparency. I had to attach them in the nib. I don't know why.

What should I do, to get better results? Can I draw the NSTextView directly into an image in some way? Or will I need to use another technique. I still want to keep the control of attributes, size, font, and the automatic wrapping that the text view has, because that's a big part of what I'm trying to do. Here's the relevant code:

    

float xPosition = [newElement xPosition];
		float yPosition = [newElement yPosition];
		float elementWidth = [newElement width];
		float elementHeight = [newElement height];
		
		xPosition = (xPosition / 640) * [self bounds].size.width;
		yPosition = (yPosition / 480) * [self bounds].size.height;
		elementWidth = (elementWidth / 640) * [self bounds].size.width;
		elementHeight = (elementHeight / 480) * [self bounds].size.height;
		
		NSLog(@"%f by %f", elementWidth, elementHeight);
		
		NSTextView * textView = self superview] textView];
		[[NSPanel * textPanel = self superview] textPanel];
		
		[[NSRect newFrame = NSMakeRect(0,0,elementWidth,elementHeight);
		
		[textView setFrame:newFrame];
		[textPanel setFrame:NSMakeRect(-10000.0,-10000.0,800,600) display:YES];

		[textPanel orderFront:self];
		
		[textView setEditable:NO];
		[textView setSelectable:NO];
		[textView setBackgroundColor:[NSColor clearColor]];
		[textView setString:[newElement textElement]];
		
		NSMutableDictionary * attributes = [[NSMutableDictionary alloc] init];

		[attributes setObject:[newElement textFont] forKey:NSFontAttributeName];
		[attributes setObject:[newElement textColor] forKey:NSForegroundColorAttributeName];
		[attributes setObject:[newElement backgroundColor] forKey:NSBackgroundColorAttributeName];
		[attributes setObject:[newElement elementShadow] forKey:NSShadowAttributeName];
		
		[textView setString:[newElement textElement]];
		textView textStorage] setAttributes:attributes range:[[NSMakeRange(0,textView textStorage] length])];
		
		[[NSBitmapImageRep * saveImage;
		[textView lockFocus];
		saveImage = [[NSBitmapImageRep alloc] initWithFocusedViewRect:[textView frame]];
		[textView unlockFocus];
		NSImage * image = [[NSImage alloc] initWithData:[saveImage TIFFRepresentation]];

		[textView setEditable:YES];
		
		[textPanel orderOut:self];
		
		if ([image isValid]) {
			NSRect imageRect;
			NSRect finalDrawingRect;
			imageRect.origin.x = 0;
			imageRect.size = [image size];
			finalDrawingRect = NSMakeRect(xPosition, yPosition, [image size].width, [image size].height);
			[image drawInRect:finalDrawingRect
					 fromRect:imageRect
					operation:NSCompositeSourceOver
					 fraction:1.0];
			
			if ([newElement isEqual:selectedElement]) {
				NSBezierPath * tracePath = [NSBezierPath bezierPathWithRect:finalDrawingRect];
				[[NSColor keyboardFocusIndicatorColor] set];
				[tracePath setLineWidth:3.0];
				[tracePath stroke];
			}
		}
		
		[image autorelease];



----
Look at     - (NSData *)dataWithPDFInsideRect:(NSRect)rect; or     - (id)initWithFocusedViewRect:(NSRect)rect;.
----

I'm guessing you didn't look at the code. It currently uses initWithFocusedViewRect:
But I think that might be the source of my troubles. Would there be any way to draw an NSTextView into the image?

----
What about the other suggestion?. Those are both ways to draw an NSTextView into an image. A third way, if you can require Tiger, would be to use     - (BOOL)lockFocusIfCanDrawInContext:(NSGraphicsContext *)context;. I think that the PDF technique and this one will both preserve transparency.

----

Can I draw the NSTextView directly into these target rects,  or should I still be using the NSPanel? Does the NSTextView need to be somewhere on the screen (a subview of something), before it can be grabbed in a lockFocus on its rect, with the NSGraphicsContext?

... okay, I found the answer to my own question. I've managed to get the text view to appear in the image, via the PDF technique, but there's a weird bug where my programmatically created text view (not a subview of anything) won't show all of the text. If I put a paragraph of text into it (programmatically), it only shows the last line. It's wrapping, I think, but only showing the last line.

Ah, wait, correction... It's showing the first line, not wrapped. But I want it wrapped.

... heh. Never mind, guys. I fixed my own problem with the wrapping. Thanks for all your help, everyone! I don't know what I would do without the CocoaDev community!

