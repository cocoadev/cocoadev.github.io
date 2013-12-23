---
layout: page
title: VerticallyCenteringNSTextFieldCell
---



I'm have an General/NSMatrix with some General/NSTextFieldCells, and I'd like to center the text inside each cell both vertically and horizontally, but it doesn't seem to be working. I figured that I could use some General/NSAttributedStrings to do this, but for some reason it's not working. Here's my code.

    
General/NSTextFieldCell *cell = [grid cellAtRow:0 column:0]; // grid is an General/NSMatrix
General/NSFont *stringFont = General/[NSFont fontWithName:@"Lucida Grande" size:12.0];
General/NSMutableParagraphStyle *stringStyle = General/[[NSMutableParagraphStyle defaultParagraphStyle] mutableCopy];
[stringStyle setAlignment:General/NSCenterTextAlignment];
General/NSArray *attrib = General/[NSArray arrayWithObjects:stringFont,General/[NSNumber numberWithFloat:10.0],stringStyle,nil];
General/NSArray *attribnames = General/[NSArray arrayWithObjects:General/NSFontAttributeName,General/NSBaselineOffsetAttributeName,General/NSParagraphStyleAttributeName,nil];
General/NSDictionary *stringAttributes = General/[NSDictionary dictionaryWithObjects:attrib forKeys:attribnames];
General/NSAttributedString *string = General/[[NSAttributedString alloc] initWithString:@"A" attributes:stringAttributes];
[cell setAttributedStringValue:string];


The horizontal centering is working fine, but I've tried many values (possitive, negative, big, small) for General/NSBaselineOffsetAttributeName but it doesn't seem to have any effect at all. Can someone tell me what am I doing wrong?

Thanks in advance,
- General/PabloGomez

----

See http://www.cocoadev.com/index.pl?General/VerticallyCenteringTableViewItems

----

I wanted to avoid subclassing General/NSTextFieldCell. Isn't there a way to do it without subclassing?
Nevermind, I finally gave up and decided to just go ahead and subclass General/NSTextFieldCell as you suggested. Thanks for helping.
