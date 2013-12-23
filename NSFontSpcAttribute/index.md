---
layout: page
title: NSFontSpcAttribute
---

I'm working on a NSView subclass that needs to draw complete lines of text (i.e. not clipped off the top & bottom edges of the view.) So I need to figure out how many lines of text in a given font can fit within my view's height.

I have a NSFont called     theFont. I'm using 18 point Zapfino to test with.

If my view is 859 points high,     [theFont defaultLineHeightForFont] gives a value of 61.00000. I'm calculating the maximum line count like     floor(859.0 / 61.00) which is 14, but testing shows my view can only hold 12 lines of text as drawn with     [myString drawAtPoint:NSMakePoint(0.0,0.0) withAttributes:attrs]. WTF?? Obviously the actual view is more complex (but not much) but I think that's all the relevant information.

I've also used     [oneLineOfMyString sizeWithAttributes:attrs].height] which gives better results, but the size returned from this varies depending on the string's contents, and I have to have the string handy to do it. Not ideal.

    [theFont description] outputs something like this     "CGS Zapfino 18.00 pt. P [] (0x007b4f30) fobj=0x007b50a0, spc=9.00".

Now, 61.00 + 9.00 = 70, which gives a max line value of 12, which is what I'm looking for. But how can I get at the 'spc' value programatically? I'm guessing it's the default leading applied to the font, but scouring the docs didn't turn up anything, and I've had no luck using Carbon and ATSFontMetrics. (I just get zero for the leading.)

SO: Is there a way to get a NSFont's 'spc' attribute from Cocoa?

And what is the best way to calculate the # of lines a view can hold?

----

Don't know if this will be of any help but Apple just released an updated article on Font Handling (dated 08-31-2004). You can find it at: http://developer.apple.com/documentation/Cocoa/Conceptual/FontHandling/index.html

----

Thanks, but I've been all over that. The Getting Font Metrics page is conspicuously silent when it comes to retrieving the leading information (if that is the same thing as the font's spc... I'm not sure it is.) Simply subtracting (ascender + descender) from the default line height doesn't seem to give the right value.

----

It's because descender is negative, so you must substract (ascender - descender). I discovered it the hard way, and the double arrowed line on the figure in "Font Handling" is not hint for this.

OlivierScherler

----

Well, I've implemented (perpetrated is a better word) the following method in a category on NSFont. Please shoot me.

    

-(float)linePadding
{
	NSString *descriptionString = self description] copy];
	[[NSScanner *floatScanner = [NSScanner scannerWithString:descriptionString];
	float padding;
	
	[descriptionString release];
	[floatScanner scanUpToString:@"spc=" intoString:nil];
	[floatScanner scanString:@"spc=" intoString:nil];
	
	if ([floatScanner scanFloat:&padding])
	{
		return padding;
	}
	else
	{
		return 0.0;
	}
}


