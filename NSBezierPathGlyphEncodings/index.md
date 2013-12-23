---
layout: page
title: NSBezierPathGlyphEncodings
---


Describe NSBezierPathGlyphEncodings here.

I'm having to write a small Cocoa app (a Sudoku puzzle generator) for the publishing company I work for.
This app generates PDFs and they have to be "Print ready", that is the black only being one colour (K in CMYK) and no TrueType fonts used.

The first requirement was no problem, but the second, in order to get around the possibility that the user was using a TrueType or dfont version of the typeface used (Helvetica),
I decided to render all text in the PDfs as beziers.

The following draws the bezier paths in a subclassed NSView
    
-(void) drawBezierPathFromString:(NSString*)strForBezier usingFont:(NSFont*)font inColor:(NSColor*)theColor inRect:(NSRect)pathRect
{
	
	NSBezierPath *path = [NSBezierPath bezierPath];
	int n, len;
	len = [strForBezier length];
	float capHeight = [font capHeight];
	NSPoint startPoint;
	startPoint.x = pathRect.origin.x;
	startPoint.y = pathRect.origin.y + capHeight;
	
	[theColor set];
	
	[path moveToPoint:startPoint];
	for(n = 0; n < len; n++)
	{
		[path appendBezierPathWithGlyph:[strForBezier characterAtIndex:n]-29 inFont:font];
	}
	[path fill];
}


This works as it should except that I have to offset the int returned by 'characterAtIndex:n' by -29 in order render the glyph I expect.
This workaround will probably cause the app to break at some future point or produce Sudoku puzzles full of meaningless characters.

What am I missing about font encodings?

Simon Ward
simon.ward@optusnet.com.au

----

As I understand it, glyph numbers are arbitrary and have no global meaning. Instead of hardcoding the transformation, which as you rightly note will probably break, you should create an NSLayoutManager, feed it with the characters you want to use, and then ask it for the corresponding glyphs.

----

Here is a code snippet that I use to produce font-free PDFs for foil cutting.
This code grew from pieces gathered on the net on some of my own experiments.
It takes the text and font from a NSTextView *textView (and supports only one font right now,
but this is not too hard to fix - please add the code here if you do it).

BjoernKriews

    

    NSFont *font = [textView font];
    
    NSBezierPath *path = [NSBezierPath bezierPath];

    NSRange effectiveGlyphRange;

    NSLayoutManager *layoutManager = [textView layoutManager];
    NSTextContainer *textContainer = layoutManager textContainers] objectAtIndex: 0];

    [layoutManager setUsesScreenFonts:NO];

    [[NSRange glyphRange = [layoutManager glyphRangeForTextContainer:textContainer];

    NSGlyph *glyphs = malloc (sizeof(NSGlyph) * glyphRange.length );

    for( int i = glyphRange.location ; i < glyphRange.length ; ++i ) {
        
        NSGlyph g = [layoutManager glyphAtIndex: i];
        
        if( [layoutManager notShownAttributeForGlyphAtIndex: i] == YES || [layoutManager isValidGlyphIndex: i] == NO) 
            continue;
        
        NSRect lineRect = [layoutManager lineFragmentRectForGlyphAtIndex: i effectiveRange: &effectiveGlyphRange];
        
        NSPoint loc = [layoutManager locationForGlyphAtIndex: i];
        
        [path moveToPoint: NSMakePoint( lineRect.origin.x + loc.x, [self bounds].size.height - ( lineRect.origin.y + loc.y))];
        [path appendBezierPathWithGlyphs: &g count: 1  inFont: font];
    }


----
Hmm,

I looked into this and decided that it was too complex and had too much overhead for my more basic needs (single digit strings and a short filename string).  I just needed a way to map Unicode characters in the range of 32-127.  Not that I'm knocking what's been proposed here. NSLayoutManager has tremendous scope, but my needs are a lot simpler.

I went back to how an Adobe Roman font writes out the glyph names as an array.  The font then accesses those names by indexing that array and treats the returned string as an executable name.  It's very easy to construct an object that can return a glyph name from an array index, present this to the font and get a glyph and then present that to an NSBezierPath object to create a path.

Simon Ward

