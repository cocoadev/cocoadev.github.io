---
layout: page
title: DynamicallySizingScalingText
---

see also DetermineSizeOfDrawnString

I came up with some code I thought was neat and thought I'd share...  I have a custom NSView subclass that I want to display a string (myText) that as I resize the view (say by anchoring it to the window and changing window size) tries to make sure the text stays vertically/horizontally centered and all visible by scaling the font down if the view gets too small... I'll just show the over-ridden drawRect: method (to change default/minimum font sizes adjust fontMax/fontMin)... This code has been reformatted for your viewing pleasure ;P (update August 2, 2004.. adjustment for a single word that is longer than the width of the view to help it scale without wrapping.

    
-(void)drawRect:(NSRect)rect
{
	[self lockFocus];
	
	NSMutableDictionary* textAttributes =  
		[NSMutableDictionary dictionary];
	NSMutableParagraphStyle* paraStyle = 
		[[NSMutableParagraphStyle alloc] init];
	[paraStyle setAlignment:NSCenterTextAlignment];
	[textAttributes setObject:paraStyle forKey:NSParagraphStyleAttributeName];

	float fontMax = 16.0, fontMin = 8.0;
	
	// calculate font size
	NSFont* theFont = [NSFont userFontOfSize:fontMax];
	while( 
		// the font size won't fit in the view 
		// and font size is more than the minimum
			  (text componentsSeperatedByString:@" "] count] == 1) 
			  ? (
			   [font widthOfString:text]
			   > rect.size.width
			  ):(
				 (
				  [font widthOfString:text] 
				  / rect.size.width
			      + 1.0
				 ) 
			     * [font defaultLineHeightForFont] 
			     > rect.size.height
			   )
			   && [font pointSize] > fontMin
		) {
		// step the font size down
		theFont = [[[NSFont userFontOfSize:([theFont pointSize]-1)];
	}
	
	// set text rectangle
	[textAttributes setObject:theFont forKey:NSFontAttributeName];
	NSSize textSize = [myText sizeWithAttributes:textAttributes];
	NSPoint textOffset;
	NSRect textRect;

	if(textSize.width > rect.size.width) {
		// it's too wide to fit on one line
		
		textSize.width = rect.size.width;
		
		textSize.height = // chars/(chars/line) * line height + one line
			(float)[myText length] 
			/ (
				rect.size.width 
				/ [theFont boundingRectForFont].size.width
			)
			* [theFont defaultLineHeightForFont] 
			+ [theFont pointSize];		
	}		

	//center the rect
	textOffset.x = 
		(rect.size.width / 2.0) - (textSize.width / 2.0);
	textOffset.y = 
		(rect.size.height / 2.0) - (textSize.height / 2.0);
	
	textRect.size = textSize;
	textRect.origin = textOffset;
	
	// draw the string
	[myText drawInRect:textRect withAttributes:textAttributes];

	[[self superview] setNeedsDisplay:YES];	
	[self unlockFocus];
}



