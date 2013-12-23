---
layout: page
title: NSStateMarkerBug
---

_NSStateMarker is something that I didn't even know existed until recently, and I still don't know what it does, but it's causing a weird bug in my app.
From Google searches, I've found that the class points out selection information for NSArrayControllers.
I modified the GraphicBindings example by mmalc to work as a Keynote-like slide editor, and I've bound objects and colors to each slide. For example, I just copied the existing methods for binding my NSColor value to the NSView subclass:

    
- (NSObject *)colorContainer
{
    return colorContainer; 
}
- (void)setColorContainer:(NSObject *)aColorContainer
{
    if (colorContainer != aColorContainer) {
        [colorContainer release];
        colorContainer = [aColorContainer retain];
    }
}

- (NSString *)colorKeyPath
{
    return colorKeyPath; 
}
- (void)setColorKeyPath:(NSString *)aColorKeyPath
{
    if (colorKeyPath != aColorKeyPath) {
        [colorKeyPath release];
        colorKeyPath = [aColorKeyPath copy];
    }
}

- (void)bind:(NSString *)bindingName
	toObject:(id)observableObject
 withKeyPath:(NSString *)observableKeyPath
	 options:(NSMutableDictionary *)options
{
	//NSLog(@"binding %@", bindingName);
    if ([bindingName isEqualToString:@"graphics"])
	{
		
		[self setGraphicsContainer:observableObject];
		[self setGraphicsKeyPath:observableKeyPath];
		[graphicsContainer addObserver:self
							forKeyPath:graphicsKeyPath
							   options:(NSKeyValueObservingOptionNew |
										NSKeyValueObservingOptionOld)
							   context:GraphicsObservationContext];
		[self startObservingGraphics:[graphicsContainer valueForKeyPath:graphicsKeyPath]];
		
    }
	else if ([bindingName isEqualToString:@"selectionIndexes"])
	{
		[self setSelectionIndexesContainer:observableObject];
		[self setSelectionIndexesKeyPath:observableKeyPath];
		[selectionIndexesContainer addObserver:self
									forKeyPath:selectionIndexesKeyPath
									   options:nil
									   context:SelectionIndexesObservationContext];
    }
	else if ([bindingName isEqualToString:@"background"])
	{
		[self setBackgroundContainer:observableObject];
		[self setBackgroundKeyPath:observableKeyPath];
		[backgroundContainer addObserver:self
									forKeyPath:backgroundKeyPath
									   options:nil
									   context:BackgroundObservationContext];
    }
	else if ([bindingName isEqualToString:@"backgroundScaling"])
	{
		[self setBackgroundScalingContainer:observableObject];
		[self setBackgroundScalingKeyPath:observableKeyPath];
		[backgroundScalingContainer addObserver:self
							  forKeyPath:backgroundScalingKeyPath
								 options:nil
								 context:BackgroundScalingObservationContext];
    }
	else if ([bindingName isEqualToString:@"color"])
	{
		[self setColorContainer:observableObject];
		[self setColorKeyPath:observableKeyPath];
		[colorContainer addObserver:self
									forKeyPath:colorKeyPath
									   options:nil
									   context:ColorObservationContext];
    }
	
	[super bind:bindingName
	   toObject:observableObject
	withKeyPath:observableKeyPath
		options:options];
	
    [self setNeedsDisplay:YES];
}



So here's what happens:
everything is fine and dandy until the user goes and clicks on the desktop or another window (taking away the focus from the document and view), and then tries to refocus on the application (thus calling my view's displayRect: method). At that point, my drawing code calls self color] set], but that no longer returns the [[NSColor, but instead returns a copy of NSStateMarker. That brings my app's drawing to a grinding halt until I figured out that I must change the selection in the NSArrayController (by clicking on the table view). After doing that, it is able to draw again... until I click outside of that document window again. Why would the NSArrayController lose the selected object when I click outside of the window? or, is there some way that I can get useful information out of that NSStateMarker for me to figure out what's going on?

----
I don't see any of your drawing code here, nor the implementation of     [self color], wouldn't it be better to actually include the code that's going wrong?

----
Okay, here it is, and very long... don't say I didn't warn you ;)

    

- (void)drawRect:(NSRect)rect
{
	
	if (self color] isMemberOfClass:[_NSStateMarker class) {
		NSLog(@"bindings bug");
		self superview] updateSelection];
		//return;
	}
	if ([[self background] isMemberOfClass:[_NSStateMarker class) {
		NSLog(@"bindings bug");
		self superview] updateSelection];
		//return;
	}
	
	wratio = 800 / [self bounds].size.width;
	hratio = 600 / [self bounds].size.height;
	
	[[NSRect myBounds = [self bounds];
	/*NSDrawLightBezel(myBounds,myBounds);
	
	NSBezierPath *clipRect =
		[NSBezierPath bezierPathWithRect:NSInsetRect(myBounds,2.0,2.0)];
	[clipRect addClip];	*/
	
	self color] set];
	[[NSRectFill(myBounds);
	
	if (self background] previewImage]) {
		float framewidth = [self bounds].size.width;
		float frameheight = myBounds.size.height;
		// Draw the preview image of the background file...
		//[[NSImage * drawImage = [[NSImage alloc] initWithSize:rect.size];
		//[drawImage setFlipped:YES];
		//[drawImage lockFocus];
		if ([backgroundScaling isEqualToString:@"Proportional to Fit"]) {
			// make sure the image scales up in proportion until hitting the inner bounds of the slide
			float imagewidth = [self background] previewImage] size].width;
			float imageheight = [[[self background] previewImage] size].height;
			float imageRatio = imagewidth / imageheight;
			//[[NSLog(@"image ratio: %f", imageRatio);
			float finalwidth = 50;
			float finalheight = 50;
			
			if (imagewidth > imageheight) {
				// image is wide
				if (framewidth > frameheight) {
					finalwidth = framewidth;
					finalheight = finalwidth / imageRatio;
				}
			} else if (imageheight > imagewidth) {
				// image is tall
				if (framewidth > frameheight) {
					finalheight = frameheight;
					finalwidth = finalheight * imageRatio;
				}				
			} else {
				// image is square or something...
				if (framewidth > frameheight) {
					finalheight = frameheight;
					finalwidth = finalheight * imageRatio;
				}				
			}
			
			float xmidpoint = (framewidth / 2) - (finalwidth / 2);
			float ymidpoint = (frameheight / 2) - (finalheight / 2);
			
			[self background] previewImage] drawInRect:[[NSMakeRect(xmidpoint,ymidpoint,finalwidth,finalheight) fromRect:NSMakeRect(0,0,[self background] previewImage] size].width,[[[self background] previewImage] size].height) operation:[[NSCompositeSourceOver fraction:1.0];
			
		} else if ([backgroundScaling isEqualToString:@"Stretch to Fit"]) {
			// stretch/distort the image to fit the bounds of the slide
			[self background] previewImage] drawInRect:[[NSMakeRect(0,0,rect.size.width,rect.size.height) fromRect:NSMakeRect(0,0,[self background] previewImage] size].width,[[[self background] previewImage] size].height) operation:[[NSCompositeSourceOver fraction:1.0];
		} else if ([backgroundScaling isEqualToString:@"Actual Size"]) {
			// show the image at its original pixel size in the slide
			int width = [self background] previewImage] size].width;
			int height = [[[self background] previewImage] size].height;
			float xmidpoint = (rect.size.width / 2) - (width / 2);
			float ymidpoint = (rect.size.height / 2) - (height / 2);
			
			[[[self background] previewImage] drawInRect:[[NSMakeRect(xmidpoint,ymidpoint,width,height) fromRect:NSMakeRect(0,0,width,height) operation:NSCompositeSourceOver fraction:1.0];
		}
		
		//[drawImage unlockFocus];
		//[drawImage drawInRect:rect fromRect:NSMakeRect(0,0,rect.size.width,rect.size.height) operation:NSCompositeSourceOver fraction:1.0];
	}
	
	// draw "intersect lines" for mouse...
	//NSLog(@"drawing");
	NSRect horizontalLine = NSMakeRect(0.0,mousePoint.y,[self bounds].size.width,1.0);
	NSRect verticalLine = NSMakeRect(mousePoint.x,0.0,1.0,[self bounds].size.height);
	/*if (!dragging) {
		[[NSColor lightGrayColor] set];
		[NSBezierPath fillRect:horizontalLine];
		[NSBezierPath fillRect:verticalLine];
	}*/
	
	
	/*
	 Draw graphics
	 */
	NSArray *graphicsArray = [self graphics];
	//NSLog(@"number of graphics: %d", [graphicsArray count]);
	NSEnumerator *graphicsEnumerator = [graphicsArray objectEnumerator];
	// apparently, when i merely declare the protocol on NSObject, it doesn't work, so we need to specify "element"...
	element *graphic;
    while (graphic = [graphicsEnumerator nextObject])
	{
		wratio = 800 / [self bounds].size.width;
		hratio = 600 / [self bounds].size.height;
		NSRect graphicDrawingBounds = [graphic drawingBounds];
		
		[graphic setWRatio:wratio];
		[graphic setHRatio:hratio];
		[graphic drawInView:self];
    }
	
	
	// filling the dragRect, when dragging...
	
	// color separation courtesy of Wil Shipley...
	// Take the color apart
	NSColor *alternateSelectedControlColor = [NSColor
		alternateSelectedControlColor];
	float hue, saturation, brightness, alpha;
	alternateSelectedControlColor
      colorUsingColorSpaceName:[[NSDeviceRGBColorSpace] getHue:&hue
												  saturation:&saturation brightness:&brightness alpha:&alpha];
	
	// Create synthetic darker and lighter versions
	NSColor *lighterColor = [NSColor colorWithDeviceHue:hue
											 saturation:MAX(0.0, saturation-.12) brightness:MIN(1.0,
																								brightness+0.30) alpha:0.1];
	NSColor *darkerColor = [NSColor colorWithDeviceHue:hue
											saturation:MIN(1.0, (saturation > .04) ? saturation+0.12 :
														   0.0) brightness:MAX(0.0, brightness-0.045) alpha:alpha];
	
	[lighterColor set];
	[NSBezierPath fillRect:dragRect];
	[darkerColor set];
	NSBezierPath * path = [NSBezierPath bezierPathWithRect:dragRect];
	[path setLineWidth:0.3];
	[path stroke];
	
	
	
	/*
	 Draw a box around items in the current selection.
	 Selection should be handled by the graphic, but this is a
	 shortcut simply for display.
	 */
	
	NSIndexSet *currentSelectionIndexes = [self selectionIndexes];
	if (currentSelectionIndexes != nil)
	{
		NSBezierPath *path = [NSBezierPath bezierPath];
		unsigned int index = [currentSelectionIndexes firstIndex];
		while (index != NSNotFound)
		{
			graphic = [graphicsArray objectAtIndex:index];
			NSRect graphicDrawingBounds = [graphic drawingBounds];
			if (NSIntersectsRect(rect, graphicDrawingBounds))
			{
				[path appendBezierPathWithRect:graphicDrawingBounds];
			}
			index = [currentSelectionIndexes indexGreaterThanIndex:index];
		}
		
		[[NSColor blueColor] set];
		[path setLineWidth:5];
		[path stroke];
		
	}
}



----
I still don't see the implementation for     [self color]. Is it this non-obvious what the important code is, even when it's pointed out?

----
Well, that probably doesn't do much, but okay:

    

- (NSColor *)color
{
	return [colorContainer valueForKeyPath:colorKeyPath];
}



... wouldn't it raise an error at this point if the data it recieves isn't the same as what it expects (seeing as how I told it to expect NSColor)?

----
*You* know that it doesn't do much. *I* didn't know this until you posted it, and even though it doesn't do much I had no idea what not-much it actually did until you showed the code. You're having trouble solving this problem with the entire app in front of you; imagine how difficult it is for everybody else, seeing only bits of the code. Showing every relevant piece is essential.

The NSColor * is purely a compile time construct that is used by the compiler to do some very basic static checking on your code. There are no runtime consequences to assigning an object of one type to a variable of another.

As far as your problem, it's either memory management or somebody is changing the value for that key path. Use NSZombieEnabled to search for the former, and see if you can poke around in the debugger and use logging to search for the latter.

