---
layout: page
title: SimpleOnTheFlyTiffs
---

I'm writing a Cocoa class to manage newspaper ads and would like to provide a method to generate very simple placeholder TIFFs for ads that have not been completed yet. The tiffs aren't for display in my app (they'll be imported into Quark picture boxes), so they don't need a window or view during creation. 

I read the NSGraphicsContext documentation along with the NSBezierPath but can't figure out how to set up a context and draw to it. 

The NSGraphicsContext documentation says that it accepts NSData objects as drawing targets but I keep getting "Unsupported printing format specified" and "Currently only pathnames are allowd for NSPersistentGraphicsContext" in my attempts.

Basically here's what I'm trying to do:
- Draw a border around the ad rectangle
- Draw 3 or 4 lines of styled text centered horizontally and vertically in the ad rectangle

And here's the create placeholder method so far (such as it):
    
- (void) placeholderImage
{
	NSMutableData			*data;
	NSDictionary			*contextAttributes;
									
	NSGraphicsContext		*placeholderContext,
					*inContext;
						
	BOOL				inAntialias;
	NSImageInterpolation		inInterpolation;

	int				centH   = [self height] / 2,
					centV   = [self width] / 2;
					
	NSRect				bounds  = { {0, 0}, {[self height], [self width]} };
	NSBezierPath			*temp   = [NSBezierPath bezierPathWithRect: bounds];
	
	
	data				= [[NSMutableData alloc] init];
	contextAttributes		= [NSDictionary dictionaryWithObjectsAndKeys: 
						data, NSGraphicsContextDestinationAttributeName,
						NSGraphicsContextPDFFormat, NSGraphicsContextRepresentationFormatAttributeName,
						nil];
									
	placeholderContext		= [NSGraphicsContext graphicsContextWithAttributes: contextAttributes],
	inContext			= [NSGraphicsContext currentContext];
						
	inAntialias			= [inContext shouldAntialias];
	inInterpolation			= [inContext imageInterpolation];
	
	// set up the drawing environment
	[placeholderContext setShouldAntialias: YES];
	[placeholderContext setImageInterpolation: NSImageInterpolationHigh];
	[placeholderContext saveGraphicsState];
	
	self color] setStroke];
	
	[[[[NSColor blackColor] setStroke];
	
	[temp setLineWidth: 5];
	[NSBezierPath strokeRect: bounds];
	
	[data writeToFile: @"System (OS X)/Users/kentozier/Desktop/bobo.pdf" atomically: YES];
	[inContext restoreGraphicsState];
	[inContext setShouldAntialias: inAntialias];
	[inContext setImageInterpolation: inInterpolation];
}


Thanks for any help,

Ken

----

I think you're being over-complicated. You can draw into NSImage, so simply create a new NSImage, lockFocus on it, draw, unlockFocus, and ask it for some TIFF data.

----

I wasn't aware that you could draw text to an NSImage. How would you go about that? 

*The same way you'd draw text into a view. Lock focus on the image (this is normally done for you with a view), draw the text using NSString or NSAttributedString methods, unlock focus. Once you've done lockFocus on the image, all drawing can be done normally.*

----

also, that path is wrong. remove 'System (OS X)' and it will be correct. *--boredzo*

**And instead of relying on the current user being kentozier...     [@"~/Desktop/bobo.pdf" stringByExpandingTildeInPath]**

----

Here's an example of drawing into an image:

    
- (NSImage *) placeholderImageOfSize:(NSSize)aSize
{
    NSString  *text = @"Placeholder Text";
    NSDictionary *textAttributes = nil; // Lucida Grande 13pt. see NSAttributedString for dict keys...

    NSSize     textSize = [text sizeWithAttributes:textAttributes];

    NSPoint   midpoint = NSMakePoint(aSize.width / 2.0, aSize.height / 2.0);
    NSPoint   textOrigin = NSMakePoint(midpoint.x - (textSize.width / 2.0), midpoint.y - (textSize.height / 2.0));

    NSImage *placeholder = [[NSImage alloc] initWithSize:aSize];

    [placeholder setFlipped:YES]; // text likes to be drawn in flipped coords...
    [placeholder lockFocus];

    [text drawAtPoint:textOrigin withAttributes:textAttributes];

    [placeholder unlockFocus];

    return [placeholder autorelease];
}

// To actually save the tiff file out ...
- (void)saveTiffFileOut
{
   // Assumes someSize is a valid NSSize
   // Assumes path is a valid file path like @"~/Desktop/MyPic.tiff"
   [self placeholderImageOfSize:someSize] [[TIFFRepresentation] writeToFile:path atomically:YES];
}


