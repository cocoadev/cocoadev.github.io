---
layout: page
title: InsertHyperlink
---



This may be a stupid newbie question but i could not find anything on the net.

How can i insert a NSTextField with a clickable (blue and underlined) hyperlink in it?
I just want to have a little text on the bottom of my app with a clickable hyperlink to my website.

----

You could try an NSButton.  Set it as borderless and style the text to look like a link and then have an action that opens the link using NSWorkspace.  Or, possibly a bit easier, drag out an NSTextView, resize however you want (turn off scrollers to make it resize smaller), and then copy and paste a link into it from a webpage or from TextEdit.

----
And with code, something like this
    
[myTextField setAllowsEditingTextAttributes: YES];
NSMutableAttributedString *mAttr = myTextField attributedStringValue] mutableCopy];
[[NSDictionary *dict = [NSDictionary dictionaryWithObject: @"http://your.link.com/page" forKey: NSLinkAttributeName];
NSRange range = NSMakeRange(0, [mAttr length];
[mAttr addAttributes: dict range: range)];
[myTextField setAttributedStringValue: mAttr];


----

thanks. i chose the button and it workes fine - just as intended!

----

You could subclass NSButton like so, then set it as your button's class in IB. Set the button's title in IB too, either directly in Attributes, or via bindings. You don't need to bind the button's action, since it's done in the code below.

    
// HyperlinkButton.h

#import <Cocoa/Cocoa.h>
@interface HyperlinkButton : NSButton
- (IBAction) openURL:(id) sender;
@end

----

// HyperlinkButton.m

#import "HyperlinkButton.h"

@implementation HyperlinkButton

- (void) awakeFromNib {
	self.isBordered = NO;
	[self setBezelStyle:NSRegularSquareBezelStyle];
	[self setButtonType:NSMomentaryChangeButton];

	NSURL *url = [NSURL URLWithString:self.title];
	NSDictionary *attributes = [NSDictionary dictionaryWithObjectsAndKeys:
								[NSNumber numberWithInt:NSUnderlineStyleSingle], NSUnderlineStyleAttributeName,
								url, NSLinkAttributeName,
								[NSFont systemFontOfSize:[NSFont smallSystemFontSize]],  NSFontAttributeName,
								[NSColor colorWithCalibratedRed:0.1 green:0.1 blue:1.0 alpha:1.0], NSForegroundColorAttributeName,
								nil];
	self.title = url.absoluteString;
	self.attributedTitle = [[NSAttributedString alloc] initWithString:url.absoluteString attributes:attributes];
	[self sizeToFit]; // only needed if the size isn't determined at compile time, e.g., you get the URL string from NSUserDefaults
	
	self.action = @selector(openURL:);
	self.target = self;
}

- (IBAction) openURL:(id) sender {
	[[NSWorkspace sharedWorkspace] openURL:[NSURL URLWithString:self.title]];
}

- (void)resetCursorRects {
	[self addCursorRect:[self bounds] cursor:[NSCursor pointingHandCursor]];
}

@end



Apple recommend Extending NSAttributedString, worked well for me after adding a line to set the type to 13pt Lucida Grande. http://developer.apple.com/qa/qa2006/qa1487.html

