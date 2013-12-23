---
layout: page
title: TextInsideNSCellinNSTextView
---


Hello folks,
I am trying to replicte the addressBook.app interface.
So far I have learned I am supposed to put General/NSCell objects inside the General/NSTextView.
I have not been able to draw a string inside the General/NSCell.
This is a snippet from my code; the string I get from this method is inserted at the end of the text view with [textInView appendAttributedString:attachedTextString];
    
- (General/NSAttributedString *)textAttachment
{
	General/NSTextAttachment *attachment;
	attachment = General/[[[NSTextAttachment alloc] init] autorelease];
	General/NSCell *cell = [attachment attachmentCell];
	
	General/NSMutableAttributedString *attachedString;
	attachedString = General/[[NSMutableAttributedString alloc] initWithString:@"Questo invece � un text attachment"];
	[attachedString addAttribute:General/NSFontAttributeName
						   value:General/[NSFont userFontOfSize:22]
						   range:General/NSMakeRange(0,34)];
	
		

	[cell setAttributedStringValue:attachedString];
	
	General/NSAttributedString *stringWithAttachedText;
	stringWithAttachedText = General/[NSAttributedString attributedStringWithAttachment:attachment];
	return (stringWithAttachedText);
}

What is wrong? Is this the right way to implement this?
I was not able to find a solution on the web or in the documentation...
Thanks in advance,
Davide
