---
layout: page
title: NSMenuItemWordWrap
---

Apple added the ability for an NSMenuItem to have a NSAttributedString for its title (instead of a NSString) with 10.3.

I have programmatically created a NSMenuItem and passed a very long string to setAttributedTitle.  Although I have told the NSAttributedString to use word wrapping, the text is truncated; it runs off the end of the NSMenuItem.  Since Apple has deprecated NSMenuItemCell, I think I might have to dive into Carbon to find a solution.  I thought I'd check here to see if anyone had any ideas.

Here's my code:

    
NSMutableParagraphStyle *style = [[NSParagraphStyle defaultParagraphStyle] mutableCopy];
[style setLineBreakMode:NSLineBreakByWordWrapping];
[style setAlignment:NSLeftTextAlignment];

NSMutableAttributedString *attributedTitle = [[NSMutableAttributedString alloc] initWithString:reallyLongTitle
												 attributes:[NSDictionary dictionaryWithObjectsAndKeys:
												 style,NSParagraphStyleAttributeName,nil]];
			
NSMenuItem *menuItem = [[NSMenuItem alloc] initWithTitle:plainTitle action:NULL keyEquivalent:@""];	
[menuItem setAttributedTitle:attributedTitle];
[theMenu addItem: menuItem];

[menuItem release];
[attributedTitle release];


Thanks,
JoeCrow

