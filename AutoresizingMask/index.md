---
layout: page
title: AutoresizingMask
---

An autoresizing mask is used to determine how a view is resized relative to its parent view when its parent view resizes. The enumeration values are masked together.

To have a view resize to fill its container, set it to NSViewWidthSizable | NSViewHeightSizable.

To have a view float in the center, set it to NSViewNotSizable.

----

Here is a handy function to create a string from a mask value. Helpful in certain instances for debugging:

    
NSString* StringFromAutoresizingMask(NSUInteger mask)
{
	if (mask == NSViewNotSizable)
		return @"NSViewNotSizable";
	NSString *sep = @" | ";
	NSMutableString *str = [NSMutableString string];
	if (mask & NSViewWidthSizable)
		[str appendFormat:@"NSViewWidthSizable%@", sep];
	if (mask & NSViewHeightSizable)
		[str appendFormat:@"NSViewHeightSizable%@", sep];
	if (mask & NSViewMinXMargin)
		[str appendFormat:@"NSViewMinXMargin%@", sep];
	if (mask & NSViewMaxXMargin)
		[str appendFormat:@"NSViewMaxXMargin%@", sep];
	if (mask & NSViewMinYMargin)
		[str appendFormat:@"NSViewMinYMargin%@", sep];
	if (mask & NSViewMaxYMargin)
		[str appendFormat:@"NSViewMaxYMargin%@", sep];
	if ([str hasSuffix:sep])
		[str deleteCharactersInRange:NSMakeRange([str length]-[sep length], [sep length])];
	return str;
}

