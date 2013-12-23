---
layout: page
title: CopyRTFFromTextView
---

Here is a simple example of how you can copy formatted text in an NSTextView to the clipboard/pasteboard. The default copy: method only copies plain text. --KevinWojniak

    
- (IBAction)copy:(id)sender
{
	NSPasteboard *pb = [NSPasteboard generalPasteboard];
	NSTextStorage *textStorage = [self textStorage];
	NSData *rtf = [textStorage RTFFromRange:[self selectedRange]
		documentAttributes:nil];
	
	if (rtf)
	{
		[pb declareTypes:[NSArray arrayWithObject:NSRTFPboardType] owner:self];
		[pb setData:rtf forType:NSRTFPboardType];
	}
}


----
It's a good idea to put as much data as possible on the pasteboard; for example, when putting RTF data there, why not put plain text? The more types of data you can generate, the easier is it to integrate with other applications. EnglaBenny

