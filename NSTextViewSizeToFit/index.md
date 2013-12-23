---
layout: page
title: NSTextViewSizeToFit
---

Here is a category for NSTextView that allows you to easily size a text view to fit its contents:

    
@interface NSTextView (SizeToFit)

- (NSSize)minSizeForContent;

@end

@implementation NSTextView (SizeToFit)

- (NSSize)minSizeForContent
{
	NSLayoutManager *layoutManager = [self layoutManager];
	NSTextContainer *textContainer = [self textContainer];
	
	[layoutManager boundingRectForGlyphRange:NSMakeRange(0, [layoutManager numberOfGlyphs]) inTextContainer:textContainer]; // dummy call to force layout
	NSRect usedRect = [layoutManager usedRectForTextContainer:textContainer];
	NSSize inset = [self textContainerInset];
	
	return NSInsetRect(usedRect, -inset.width * 2, -inset.height * 2).size;
}

@end

