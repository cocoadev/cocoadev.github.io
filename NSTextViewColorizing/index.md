---
layout: page
title: NSTextViewColorizing
---



I've subclassed NSTextView to make commented lines green, I use this code

    
- (void) didChangeText
{
	NSLayoutManager *layoutManager = [self layoutManager];
	NSRange lineGlyphRange, lineCharRange;
	NSString *prefix;
	NSColor *color;
	NSRange range = [self rangeForUserTextChange];
	
	(void)[layoutManager lineFragmentRectForGlyphAtIndex:range.location
                            effectiveRange:&lineGlyphRange];  
	lineCharRange = [layoutManager characterRangeForGlyphRange:lineGlyphRange
                           actualGlyphRange:NULL];
	
	prefix = [self string] substringWithRange:lineCharRange]
                  tringByTrimmingCharactersInSet:[[[NSCharacterSet whitespaceCharacterSet]];
	if([prefix hasPrefix:@"#"])
		color = [NSColor colorWithCalibratedRed:(float)42/255
                           green:(float)126/255 blue:(float)49/255 alpha:1.0f];
	else
		color = [NSColor blackColor];
		
  [layoutManager addTemporaryAttributes:
    [NSDictionary dictionaryWithObjectsAndKeys:color, NSForegroundColorAttributeName, nil]
              forCharacterRange:lineCharRange];
}


the problem lies in *(void)[layoutManager lineFragmentRectForGlyphAtIndex:range.location effectiveRange:&lineGlyphRange];*

ex: when i type *#colorize*

it shows up like this *##c#co#col#colo#color#colori#coloriz#colorize*

see the problem?

tnx

----

Iv'e solved it
when *(void)[layoutManager lineFragmentRectForGlyphAtIndex:range.location effectiveRange:&lineGlyphRange]; * is called it *performs glyph generation and layout if needed.*

so i used
    
NSRange range = [self rangeForUserTextChange];
lineCharRange = [[self string]lineRangeForRange:range];

