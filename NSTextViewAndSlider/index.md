---
layout: page
title: NSTextViewAndSlider
---

I am creating an application in which a person sets the font size of a TextView through a slider. I can't seem to find a method for this. Please help.

----

You need to modify the attributes of the text storage to change the font size.  NSTextStorage is a subclass of NSMutableAttributedString, so read the docs on attributed string to see how to modify it.  Even worse, the font size isn't a standalone attribute; it's part of a compound attribute, NSFontAttributeName, that encapsulates the font name, some style info and the size.  Here's some (Warning: entirely untested!) code for changing a text view's font size, implemented as a category on NSTextView:
    
@interface NSTextView (FontSizeChanging)
- (void)resizeFontTo:(float)inNewSize;
@end

@implementation NSTextView (FontSizeChanging)
- (void)resizeFontTo:(float)inNewSize
{
	// get the text view's storage
	NSTextStorage* storage = [self textStorage];
	// find out what the current font info is
	NSFont* currFont = [storage attribute:NSFontAttributeName atIndex:0 effectiveRange:nil];
	// create a new font with the new size
	NSFont* newFont = [[NSFontManager sharedFontManager] convertFont:currFont toSize:inNewSize];
	// make a range encompassing every character
	NSRange allChars = NSMakeRange(0, [storage length]);
	// set the font attribute to the new font
	[storage addAttribute:NSFontAttributeName value:newFont range:allChars];
}
@end

One big caveat with this method is that it fails horribly if you try to use multiple fonts; it sets the entire text to the exact same font whenever it's called.  If you need it to preserve fonts while changing size then post back here and I'll fix it up a bit. -- Bo

----

I saw this in an example from the ADC website:

    
    NSFont *theFont;
    
    [fontSizeField takeIntValueFrom:fontSizeStepper];
    theFont=[contentView font];
    theFont=[[NSFontManager sharedFontManager] convertFont:theFont toSize:[fontSizeStepper intValue]];
    [contentView setFont:theFont];

Thanks for the help.

----

That works too (with the same caveat as my earlier code), but be aware that font sizes in Cocoa are of type float not int, so it really should be using -floatValue and -takeFloatValueFrom:.  You can still constrain your slider to integer values of course. -- Bo

