---
layout: page
title: DownloadingFontsInAnEPSF
---

My application needs to create EPSFs and I have a problem with including fonts in the file. There must be system calls to get a font and convert it to postscript, as dataWithEPSInsideRect: in NSView must use it, alternatively there must be a way to get the outlines that make up a specific character and use that as part of the file.

Using dataWithEPSInsideRect: is not an option as it doesn't include colour information in a way that is needed for separations, unless anyone knows differently?

Does anyone have any experience of the same problem, and how did they get round it?

Thanks in advance

Douglas

----

AppleTypeServices provide this sort of functionality. You'll probably want to look for ATSUI. Try http://developer.apple.com/documentation/TextFonts/TextFonts.html.

----

I have looked at all the Apple font stuff, to no avail. However I did find a solution that downloaded the characters as outlines rather than as fonts. It was in the really useful Cocoa Programming by Scott Anguish, Erik M Buck and Donald A Yacktman, under textRendering in Chapter 14.

The secret is to get the glyphs of the text you want, and interpret them.

In the .m file where you call the glyphing function from put the next two lines

    

@interface NSFont(Exposing_Private_AppKit_Methods)
- (NSGlyph)_defaultGlyphForChar:(unichar)theChar;
@end



then at some point add the two routines.

    

- (void) renderOutline:(char *) pString horizPosn:(float) pHoriz vertPosn:(float) pVert

{
	short i;
	NSString *tempString;
	NSBezierPath *path;
	NSGlyph *glyphArray;
	int glyphArrayLength;

	tempString = [NSString stringWithCString:pString];

//	get the length of the string
	glyphArrayLength = [tempString length];

//	Get a buffer for the glyph data
	glyphArray = malloc(sizeof(NSGlyph) * glyphArrayLength);

//	for each glyph store it in the array
	for (i=0; i<glyphArrayLength; i++) {
		glyphArray[i] = [theFont _defaultGlyphForChar:[tempString characterAtIndex:i]];
	}

//	Create a Bezier path
	path = [NSBezierPath alloc];

//	Path always starts with a moveto
	[path moveToPoint:NSMakePoint(pHoriz,pVert)];

//	Add the glyphs of the text to the path
	[path appendBezierPathWithGlyphs:glyphArray count:glyphArrayLength inFont:theFont];

//	Go through each path element and build a postcript file
	[self pathDisplay:path];

	free(glyphArray);
}

- (void) pathDisplay:(NSBezierPath *) path

{
	int i;
	int numElements;
	NSBezierPathElement pathElement;
	NSPoint pointList[5];
	char tempStr[256];

	numElements = [path elementCount];

	for(i=0;i<numElements;i++){
		pathElement = [path elementAtIndex:i associatedPoints:pointList];

		switch(pathElement){
		case NSMoveToBezierPathElement:
			sprintf(tempStr,"%.3f %.3f moveto\r", pointList[0].x, pointList[0].y);
			break;

		case NSLineToBezierPathElement:
			sprintf(tempStr,"%.3f %.3f lineto\r", pointList[0].x, pointList[0].y);
			break;

		case NSCurveToBezierPathElement:
			sprintf(tempStr,"%.3f %.3f %.3f %.3f %.3f %.3f curveto\r",pointList[0].x,pointList[0].y,pointList[1].x,pointList[1].y,pointList[2].x,pointList[2].y);
			break;

		case NSClosePathBezierPathElement:
			sprintf(tempStr,"closepath fill\r");
			break;

		default:
			break;
		}
	}
	output tempStr to file
}


