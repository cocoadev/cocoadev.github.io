---
layout: page
title: AddTraitStrikethrough
---

Is there still no easy way to create a menu item that adds strikethrough to text in a textView?

I see the option in the font panel, but I see no easy way to replicate it into a menu item in the Fonts menu of my app.

Anyone?

GarrettMurray

----

This page describes making an NSAttributedString with specific font styles - including strikethrough and underline:

http://developer.apple.com/documentation/Cocoa/Conceptual/AttributedStrings/Tasks/AccessingAttrs.html

Here's an excerpt taken from it:


*The underline attribute has only two values defined, NSNoUnderlineStyle and NSSingleUnderlineStyle, but these can be combined with NSUnderlineByWorkMask and NSUnderlineStrikethroughMask to extend their behavior. By bitwise-ORing these values in different combinations, you can specify no underline, a single underline, a single strikethrough, both an underline and a strikethrough, and whether the line is drawn for whitespace or not.


You can edit an NSView through the view's NSTextStorage object which is a subclass of NSMutableAttributedString.

Is this what you were looking for?

-- RyanBates

----

There's also a new attribute, NSStrikethroughStyleAttributeName, introduced in Panther that controls the same effect.  Anyhoo, here's a quick category on NSTextView that implements a strikethrough action method that works like the corresponding -bold: and -underline: methods.  There's 2 methods that are functionally identical (more or less), one using the Panther attribute and one using the Jaguar attribute mask. -- Bo
    
@implementation NSTextView (StrikeThrough)
// note: I tried to make these methods behave identically to how the 
// -bold: and -underline: action methods, even when they act kinda stupid.
- (IBAction)pantherOnlyStrikethrough:(id)sender
{
	// since this is an editing action,
	// it should only make a change if it's editable
	if (![self isEditable]) {
		return;
	}
	NSMutableDictionary* typAttrs = [self typingAttributes] mutableCopy] autorelease];
	if (!typAttrs) {
		// the typing attributes can be nil so create an empty dict if that's the case
		typAttrs = [[[NSMutableDictionary dictionaryWithCapacity:1];
	}
	unsigned int currStrikeStyle = typAttrs objectForKey:[[NSStrikethroughStyleAttributeName] unsignedIntValue];
	unsigned int newStrikeStyle = (currStrikeStyle == NSUnderlineStyleNone) ? NSUnderlineStyleSingle : NSUnderlineStyleNone;
	// set the typing attributes to the new style
	[typAttrs setObject:[NSNumber numberWithUnsignedInt:newStrikeStyle] forKey:NSStrikethroughStyleAttributeName];
	[self setTypingAttributes:typAttrs];
	// if there's text selected, change it to match too
	NSRange selection = [self selectedRange];
	if (selection.length > 0) {
		self textStorage] addAttribute:[[NSStrikethroughStyleAttributeName 
					value:[NSNumber numberWithUnsignedInt:newStrikeStyle] range:selection];
	}	
}

- (IBAction)jaguarCompatibleStrikethrough:(id)sender
{
	// since this is an editing action,
	// it should only make a change if it's editable
	if (![self isEditable]) {
		return;
	}
	NSMutableDictionary* typAttrs = [self typingAttributes] mutableCopy] autorelease];
	if (!typAttrs) {
		// the typing attributes can be nil so create an empty dict if that's the case
		typAttrs = [[[NSMutableDictionary dictionaryWithCapacity:1];
	}
	unsigned int currStrikeStyle = typAttrs objectForKey:[[NSUnderlineStyleAttributeName] unsignedIntValue];
	unsigned int newStrikeStyle = (currStrikeStyle & NSUnderlineStrikethroughMask) ? 
		(currStrikeStyle & (~NSUnderlineStrikethroughMask)) : (currStrikeStyle | NSUnderlineStrikethroughMask);
	// set the typing attributes to the new style
	[typAttrs setObject:[NSNumber numberWithUnsignedInt:newStrikeStyle] forKey:NSUnderlineStyleAttributeName];
	[self setTypingAttributes:typAttrs];
	// if there's text selected, change it to match too
	NSRange selection = [self selectedRange];
	// because the NSUnderline style is a compound style, we have to iterate over it
	// to avoid overwriting any plain underlining in the range
	while (selection.length > 0) {
		NSTextStorage* theText = [self textStorage];
		NSRange currRange;
		unsigned int currUStyle = theText attribute:[[NSUnderlineStyleAttributeName atIndex:selection.location
			longestEffectiveRange:&currRange inRange:selection] unsignedIntValue];
		unsigned int newUStyle = (currStrikeStyle & NSUnderlineStrikethroughMask) ? 
			(currUStyle & (~NSUnderlineStrikethroughMask)) : (currUStyle | NSUnderlineStrikethroughMask);
		[theText addAttribute:NSUnderlineStyleAttributeName value:[NSNumber numberWithUnsignedInt:newUStyle] range:currRange];
		selection = NSMakeRange(selection.location + currRange.length, selection.length - currRange.length);
	}
}
@end


----

Exactly what I was looking for, Bo, thanks a ton! --GarrettMurray

