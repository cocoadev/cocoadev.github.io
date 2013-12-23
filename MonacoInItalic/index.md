---
layout: page
title: MonacoInItalic
---

I have tried various things to make the Monaco font appear in italic, but w/o results... I have noticed that TextEdit simply disable the italic choice for this font, and also seen a mailing list reply state that Monaco is simply not drawn in italic.

Then today I played with WorldText, and guess what: it *can* apply both italic and bold to the Monaco font.

Now how is that possible?

--AllanOdgaard

----

Basically, there are two ways to draw an italicized font:

*Select the italic variant from the font family instead of the normal variant.  This gives the best-looking results but requires that the font family have an italic variant.
*Select the normal font from the family and manually skew it to look italic.  This won't look as good in general but can be done on any font.

MLTE (Multilingual Text Editor), the text engine on which WorldText is built, uses ATSUI (Apple Type Service for Unicode Imaging) to render fonts, which supports both methods, preferring the first but falling back to the second if necessary.  The Cocoa text system, however, uses CoreGraphics to render fonts, which supports only the first method.  

So, if you want to italicize a font for which there's no italic variant defined, your only two choices I see are to either call ATSUI to render it or render the font using CoreGraphics and then manually skew the rendered glyphs.  Either way, it's a lot more work for worse looking results than just choosing a font family that does include an italic variant, like Courier for example.

----

Okay, I have tried to experiment with ATSUI, because I really need that Monaco font in bold and italic, but it would seem nothing gets drawn in my view. I am using the following code:

    

- (void)drawRect:(NSRect)rect
{
   NSEraseRect(rect);
   [[NSColor blackColor] set];

   ATSUStyle style;
   OSStatus status = ATSUCreateStyle(&style);
   if(status != noErr) NSLog(@"%d: failed", __LINE__);

   Fixed size = 12;
   ATSUAttributeTag styleTags[] = { kATSUSizeTag };
   ByteCount styleSizes[] = { sizeof(Fixed) };
   ATSUAttributeValuePtr styleValues[] = { &size };
   status = ATSUSetAttributes(style, 1, styleTags, styleSizes, styleValues);
   if(status != noErr) NSLog(@"%d: failed", __LINE__);

   /* -------- */

   UniChar text[] = { 'H', 'e', 'l', 'l', 'o' };
   UniCharCount length = sizeof(text)/sizeof(text[0]);

   ATSUTextLayout layout;
   status = ATSUCreateTextLayoutWithTextPtr(
         text,
         0,          // offset
         length,     // length
         length,     // total length
         1,          // styleRunCount
         &length,    // length of style run
         &style, 
         &layout);
   if(status != noErr) NSLog(@"%d: failed", __LINE__);

   /* -------- */

   CGContextRef myCGContext = (CGContextRef)[[NSGraphicsContext currentContext] graphicsPort];
   ATSUAttributeTag theTags[] = { kATSUCGContextTag };
   ByteCount theSizes[] = { sizeof(CGContextRef) };
   ATSUAttributeValuePtr theValues[] = { &myCGContext };
   status = ATSUSetLayoutControls(layout, 1, theTags, theSizes, theValues);
   if(status != noErr) NSLog(@"%d: failed", __LINE__);

   /* -------- */

   status = ATSUDrawText(layout, 
         0, length,
         (int)round(NSMidX([self bounds]) * 65536),   // these values are
         (int)round(NSMidY([self bounds]) * 65536));  // also of type Fixed
   if(status != noErr) NSLog(@"%d: failed", __LINE__);

   status = ATSUDisposeStyle(style);
   if(status != noErr) NSLog(@"%d: failed", __LINE__);
}



None of the NSLog's report anything, but no text shows up in my NSView...

----

At present time I have not figured out why, but it seems that **if** I set a font size, it has to be around 1.000.000 points for the font to have a reasonable size (i.e. large enough for me to actually see it).

----

The Fixed type is actually a fixed-point decimal where the top 16 bits are the integer size and the bottom 16 bits are the fractional size.  So you need to multiply by 65536 in order to translate from ints to Fixeds.  When I swapped in the following code at the beginning though, It actually did draw 12 point italic Monaco:
    
	OSStatus status = ATSUCreateStyle(&style);
	if(status != noErr) NSLog(@"%d: failed", __LINE__);
	ATSUFontID monaco;
	status = ATSUFindFontFromName("Monaco", 6, kFontFullName,
							   kFontNoPlatformCode, kFontRomanScript,
							   kFontNoLanguageCode, &monaco);
	if(status != noErr) NSLog(@"%d: failed", __LINE__);
	BOOL ital = YES;
	Fixed size = 12 * 65536;
	ATSUAttributeTag styleTags[] = { kATSUSizeTag, kATSUQDItalicTag, kATSUFontTag};
	ByteCount styleSizes[] = { sizeof(Fixed), sizeof(BOOL), sizeof(ATSUFontID) };
	ATSUAttributeValuePtr styleValues[] = { &size, &ital, &monaco };
	status = ATSUSetAttributes(style, 3, styleTags, styleSizes, styleValues);


----

Thanks, I am sure I would never have found that one, as I did check and saw Fixed as a typedef for 'unsigned long' (though I did wonder why Apple would name an integer for Fixed). *FixedPoint is a standard way of representing non-integer numbers, and this is done using standard integer types in nonstandard ways.*

Everything works as it should except that I have now figured out that applying italic or bold to Monaco is really a performance killer -- it seems that this is why the scrolling of the Terminal starts to crawl when using anti-aliased Monaco together with styles and colours (although it probably would help if they didn't redraw the entire window each time a line was scrolled) -- but this new insight have given me a solution to the Terminal problem: select Lucida Sans Typewriter as font instead of Monaco, and scrolling will be much better when encountering colours or styles (although the line height seems to be a little to low, but you can't have it all).

*You can adjust the line height in Terminal's window settings panel*

**With Panther, they improved text rendering significantly, so Terminal is now much better with Monaco in bold or italic.**

