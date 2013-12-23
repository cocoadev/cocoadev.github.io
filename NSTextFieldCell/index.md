---
layout: page
title: NSTextFieldCell
---

**NSTextFieldCell**

NSTextFieldCell adds to NSCell�s text display capabilities by allowing you to set the color of both the text and its background. You can also specify whether the cell draws its background at all. All of the methods declared by this class are also declared by NSTextField, which uses NSTextFieldCell<nowiki/>s to draw and edit text.

See: http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTextFieldCell_Class/index.html

----
After some investigation, I've settled upon how to override text drawing within an NSTextFieldCell so that it appears indistinguishable from the same text drawn while the cell is being edited (through the field editor). The sample code below draws the cell's attributed string contents including hyperlinks (normally, NSTextFieldCell does not draw styled hyperlinks):

    
- (void)drawInteriorWithFrame:(NSRect)rect inView:(NSView*)controlView {
	static NSTextContainer *tc = nil;
	NSLayoutManager *lm = nil;
	NSTextStorage *ts = nil;
	
	if (tc == nil) {
		ts = [[NSTextStorage alloc] init];
		lm = [[NSLayoutManager alloc] init];
		tc = [[NSTextContainer alloc] initWithContainerSize:NSMakeSize(1e7, 1e7)];
		
		// we configure the layout manager in the way it is configured for editing.
		// these settings were established by looking at the field editor and it's associated
		// objects after installing it using the cell's editWithFrame... method.
		[tc setLineFragmentPadding:2];

		[lm setTypesetterBehavior:NSTypesetterBehavior_10_2_WithCompatibility];
		[lm setUsesFontLeading:NO];
		
		[ts addLayoutManager:lm];
		[lm addTextContainer:tc];
		
		// these are retained by our text storage...
		[lm release];
		[tc release];		
	}
	
	lm = [tc layoutManager];
	ts = [lm textStorage];
	
	// to position our text correctly and ensure that lines wrap as they do in the
	// field editor, we expand our layout rectangle width by 0 (0 inset for no border, 1 for line border, experiment for others)
	NSRect textRect = NSInsetRect(rect, 0, 0);
	
	[ts setAttributedString:self.attributedStringValue];
	[tc setContainerSize:NSMakeSize(textRect.size.width, 1e7)];
	
	// fix hyperiinks
	unsigned int length;
	NSRange effectiveRange;
	
	length = [ts length];
	effectiveRange = NSMakeRange(0, 0);
	while (NSMaxRange(effectiveRange) < length) {
		NSURL *link = [ts attribute:NSLinkAttributeName atIndex:NSMaxRange(effectiveRange) effectiveRange:&effectiveRange];
		if (link != nil) {
			NSDictionary *linkAttributes = [NSDictionary dictionaryWithObjectsAndKeys:
											[NSColor blueColor], NSForegroundColorAttributeName,
											[NSNumber numberWithInt:NSUnderlineStyleSingle], NSUnderlineStyleAttributeName,
											nil];
			
			[lm setTemporaryAttributes:linkAttributes forCharacterRange:effectiveRange];
		}
	}
	
	NSRange range, glyphRange;
	range = NSMakeRange(0, ts.length);
	glyphRange = [lm glyphRangeForCharacterRange:range actualCharacterRange:NULL];
	
	if (glyphRange.length > 0) {
		[lm drawBackgroundForGlyphRange:glyphRange atPoint:textRect.origin];
		[lm drawGlyphsForGlyphRange:glyphRange atPoint:textRect.origin];
	}
}


Important in the above code is the configuration of the layout manager. Typesetter behavior is set to 10.2 with compatibility and usesFontLeading is set to NO. The textContainer's line fragment padding is set to 2 - these settings cause lines of text to be rendered exactly as they are using the field editor. The expanded text frame should be established through experimentation. It will vary with the style of border you have set on your text field - in the case of a borderless text field, the inset is 0.

MrO

