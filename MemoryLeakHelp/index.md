---
layout: page
title: MemoryLeakHelp
---

I have a method which resizes an image and adds a drop shadow to it.
I've determined that it is leaking several NSImage instances.
I've read up on retain counts, and I think that when I retain "resizedImage," I
increase the retain count such that [resizedImage release]; doesn't
completely release it.

    

- (NSImage *)shadowedImageWithImage:(NSImage *)image
{
	[image setFlipped:YES];
	
	// Resize the image, and make sure to antialias it.
	float thumbnailHeight = [tableView rowHeight] * 0.85;
	float thumbnailWidth = (thumbnailHeight / [image size].height) * [image size].width;
	
	NSImage *resizedImage = [[NSImage alloc] initWithSize:NSMakeSize(thumbnailHeight, thumbnailWidth)];
	[resizedImage lockFocus];
	[NSGraphicsContext saveGraphicsState];
	[[NSGraphicsContext currentContext] setImageInterpolation:NSImageInterpolationHigh];
	[image drawInRect:NSMakeRect(0.0, 0.0, thumbnailWidth, thumbnailHeight) fromRect:NSMakeRect(0.0, 0.0, [image size].width, [image size].height) operation:NSCompositeCopy fraction:1.0];
	[NSGraphicsContext restoreGraphicsState];
	[resizedImage unlockFocus];
	image = [resizedImage retain];
	[resizedImage release];
	
	// Place our new image on a background	
	NSImage *imageCanvas = [[NSImage alloc] init];
	[imageCanvas setSize:[image size]];
	[imageCanvas lockFocus];
	NSRect imageFrame = NSMakeRect(0, 0, [imageCanvas size].width, [imageCanvas size].height);
	NSImage *transparencyPattern = [[NSImage alloc] initWithSize:NSMakeSize(4, 4)];
	[transparencyPattern lockFocus];
	[[NSColor colorWithCalibratedWhite:0.8 alpha:1.0] set];
	NSRectFill(NSMakeRect(0,0,2,2));
	NSRectFill(NSMakeRect(2,2,2,2));
	[[NSColor whiteColor] set];
	NSRectFill(NSMakeRect(2,0,2,2));
	NSRectFill(NSMakeRect(0,2,2,2));
	[transparencyPattern unlockFocus];
	[[NSColor colorWithPatternImage:transparencyPattern] set];
	NSRectFill(imageFrame);
	[image compositeToPoint:NSMakePoint(0, 0) operation:NSCompositeSourceOver];
	[imageCanvas unlockFocus];
	image = [imageCanvas copy];
	[transparencyPattern release];
	[imageCanvas release];
	
	NSImage *shadowCanvas = [[NSImage alloc] initWithSize:NSMakeSize([image size].width+4, [image size].height+4)];
	[shadowCanvas lockFocus];
	NSShadow *theShadow = [[NSShadow alloc] init];
	[theShadow setShadowColor:[NSColor blackColor]];
	[theShadow setShadowBlurRadius:4];
	[theShadow set];
	[image compositeToPoint:NSMakePoint(2, 1) operation:NSCompositeSourceOver];
	[shadowCanvas unlockFocus];
	image = [shadowCanvas retain];
	[theShadow release];
	[shadowCanvas release];
	
	return image;
}


-- Matt Ball

---- 

(1) That which you retain (or copy, or alloc, or obtain with new), you must release.  In the above code I see one alloc, one retain and one release.  Aloha, imbalance!  You must autorelease resizedImage.

(2) These lines
    
   image = [resizedImage retain];
   [resizedImage release];

   return image;

do not do anything.  Just return resizedImage.

To say just a bit more:  the variables image and resizedImage are pointers.  When you say     image = resizedImage, that doesn't copy the image object or anything like that, it just means that image will point to the same object that resizedImage points at.  If you were to modify resizedImage at this point, image would also be modified (they're the same object).

*Actually, you do have to autorelease     resizedImage, so you should do something like this:     return [resizedImage autorelease]; to solve the leak --JediKnil*

**Knil, read what I said. Points one and two.**

----

I'm having a problem because I can't really balance my allocs and releases. If I release every NSImage except the final one and return [shadowCanvas autorelease]; then it won't work, because the NSImage chain that shadowCanvas points to will have already been released.

-- MattBall

----

This is the purpose of autorelease - it's a delayed release.  I think you're confused though - shadowCanvas is a single image object, not a chain.  *If* it needed to hold onto the other images (which it does not), *it* would retain them.  You don't need to.

    
copy, alloc, retain, and new count: 9
release and autorelease count: 5


In every method you write (unless you're doing something complicated, which you are not here) those two numbers should be the same.

*Erm, except for accessors, init methods and dealloc methods.  In the first two you may wish to increase the retain count of an object, and in dealloc you may want to release some stuff.*

----
*There is really no reason to keep changing     image, just do something like this (below). Notice how every     alloc is balanced with a     release or     autorelease. --JediKnil*
    
- (NSImage *)shadowedImageWithImage:(NSImage *)image
{
	float thumbnailHeight = [tableView rowHeight] * 0.85;
	float thumbnailWidth = (thumbnailHeight / [image size].height) * [image size].width;
	NSSize thumbnailSize = NSMakeSize(thumbnailHeight, thumbnailWidth);
	
	BOOL oldFlipped = [image isFlipped];
	[image setFlipped:YES];
	NSImage *resizedImage = [[NSImage alloc] initWithSize:thumbnailSize];
*// From [resizedImage lockFocus]*
*// to [resizedImage unlockFocus]*
	[image setFlipped:oldFlipped];

	NSImage *imageCanvas = [[NSImage alloc] initWithSize:thumbnailSize];
*// From [imageCanvas lockFocus]*
*// to [imageCanvas unlockFocus]*
	[transparencyPattern release];

	thumbnailSize.width += 4;
	thumbnailSize.height += 4;

	NSImage *shadowCanvas = [[NSImage alloc] initWithSize:thumbnailSize];
*// From [shadowCanvas lockFocus]*
*// to [shadowCanvas unlockFocus]*
	[theShadow release];

	[resizedImage release];
	[imageCanvas release];

	return [shadowCanvas autorelease];
}


----

I'm still getting massive amounts (~200) of memory leaks from the function. Here's a more complete representation of the order of events:

First, my tableView gets the image to be displayed:
    
- (void)tableView:(NSTableView *)tableView willDisplayCell:(id)cell forTableColumn:(NSTableColumn *)tableColumn row:(int)row
{
	NSMutableArray *array = [[[NSDocumentController sharedDocumentController] currentDocument] array];
	if(array != nil) {
		if(tableColumn identifier] isEqualToString:@"titleColumn"]) {
			int indexToQueryForImage = row * 6 + 1;
			[[NSImage *image = [array objectAtIndex:indexToQueryForImage];
			image = [self shadowedImageWithImage:image];
			
			if(image != nil)
				[cell setImage:image];
		}
	}
}

shadowedImageWithImage: is using the code JediKnil posted. The cell's code is as follows:
    
- (void)dealloc {
	[image release];
	[super dealloc];
}

- (void)setImage:(NSImage *)anImage
{
	if(anImage != image && anImage != nil)
	{
	//	[image release];   <-- It was suggested that I put this here, but with it uncommented, I get a crash whenever I select a new row.    
        image = [anImage retain];
	}
}

- (void)drawWithFrame:(NSRect)cellFrame inView:(NSView *)controlView
{
	// Draw the thumbnail image
	if (image != nil) {
        NSSize	imageSize;
        NSRect	imageFrame;
		
        imageSize = [image size];
        NSDivideRect(cellFrame, &imageFrame, &cellFrame, 10 + imageSize.width, NSMinXEdge);
        if ([self drawsBackground]) {
            self backgroundColor] set];
            [[NSRectFill(imageFrame);
        }
        imageFrame.origin.x += 3;
        imageFrame.size = imageSize;
		imageFrame.origin.y = NSMinY(cellFrame) + (cellFrame.size.height - [image size].height)/2;
		
		[image drawInRect:imageFrame fromRect:NSMakeRect(0, 0, [image size].width, [image size].height) operation:NSCompositeSourceOver fraction:1.0];
	}
	// Modify the cell frame to compensate for the image
	cellFrame.origin.y = NSMinY(cellFrame) + cellFrame.size.height/4 + 2;
	
	if([self isHighlighted])
		[self setTextColor:[NSColor alternateSelectedControlTextColor]];
	else
		[self setTextColor:[NSColor textColor]];
	// Draw the cell's text normally
    [super drawWithFrame:cellFrame inView:controlView];
}



Thanks

-- MattBall

*You do need to uncomment that line -- it's essential for a proper accessor method. I looked at my method again, and it's not doing the leaking - not on its own, at least. Make sure you are using     setImage: everywhere you want to change the     image ivar. And sorry for missing your post, anonymous. I was in a hurry and didn't take it in. --JediKnil*

----

I've uncommented it, and some people on the cocoa-dev mailing list and I are trying to figure out why it's crashing. You can catch up here: http://www.cocoabuilder.com/archive/message/cocoa/2005/7/11/141442

-- MattBall

----

Ugh.  I just read through the first bunch of posts, and the people replying do not know what they're talking about. 

*I should be offended, but I completely failed to solve this problem. Sorry, Matt! --JediKnil*  **On the apple list, knil. :-) And just the first few.**

Heh, not a problem :) You definitely helped me clean up my shadowedImageWithImage method, and I think I finally understand the deal with retain and copy. Hopefully I'll get this crash resolved soon. Thanks for your help thus far.

- Matt Ball

