---
layout: page
title: NSTextFieldWithTrailingWhiteSpace
---

Is there a simple way to get an NSTextField to show trailing white space when the alignment is set to NSRightTextAlignment?

The normal behavior of an NSTextField is to hide any trailing white space when the text is right aligned.  If the vertical bar "|" represents the end of the text field then the following string: "String with trailing space " will appear as "String with trailing space|" instead of "String with trailing space |".

In most instances this is the desired behavior.  However, there are instances where I need to see the trailing whitespace.  Is there a simple way to do this?  I've tried all the word wrapping modes, they do nothing to solve this problem.  I have also tried subclassing NSLayoutManger with the following:

    
- (void)drawGlyphsForGlyphRange:(NSRange)glyphRange atPoint:(NSPoint)containerOrigin;
{	
	NSTextView *view = [self firstTextView];
	
	if (drawTrailingWhiteSpace && ([view alignment] == NSRightTextAlignment)) {
	
		NSTextContainer *container = [view textContainer];
		float padding = [container lineFragmentPadding];
		NSSize size = [container containerSize];
		
		[view setAlignment:NSLeftTextAlignment];
		NSRect rect = [self boundingRectForGlyphRange:glyphRange inTextContainer:container];
		containerOrigin.x = size.width - rect.size.width - 2*padding;
		[super drawGlyphsForGlyphRange:glyphRange atPoint:containerOrigin];
		[view setAlignment:NSRightTextAlignment];
	} else {
		[super drawGlyphsForGlyphRange:glyphRange atPoint:containerOrigin];
	}
}


where drawTrailingWhiteSpace is a settable ivar.

The idea is to get the bounding rect when the text is left aligned (which includes the trailing white space) and then right align the text manually.  This works perfectly except for one catch:  The cursor does not blink or even appear (although text selection works properly).  I have also tried a similar technique by overriding:

- (void)drawBackgroundForGlyphRange:(NSRange)glyphRange atPoint:(NSPoint)containerOrigin;

but this does not help the cursor (caret) problem.


I have also considered substituting non-breaking spaces for white space characters, but this is a little cumbersome, since I will need to convert them back to regular spaces before using the string.  Also, if there are tabs or other white space characters, as well as spaces, then the round trip conversion will loose information.  In a similar vein, is there a unicode character for flushRight that NSTextField honors?

I have searched the web extensively for a solution, but could not find one.  Does any one have any ideas?  It shouldn't be this difficult!

-- Kevin

