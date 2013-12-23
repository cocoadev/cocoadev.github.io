---
layout: page
title: NSTextViewStrangeWrappingWithCustomNSTextTabs
---



My code works fine as long as the text on the right doesn't get bigger then the text tab.

http://catsdorule.torpedobird.com/tmp/cds1.jpg

http://catsdorule.torpedobird.com/tmp/cds2.jpg

The desired effect is that it would push the text on the right over... but it doesn't.

My Code:

    I can't post this code because of wiki spam protection. http://catsdorule.torpedobird.com/tmp/code


    - (NSDictionary *)ingredientAttributes
{
	NSMutableParagraphStyle * paraStyle = TRNewMutableParagraphStyle();
	
	NSTextTab * rightTabStop = [[[NSTextTab alloc] initWithType:NSRightTabStopType location:80] autorelease];
	NSTextTab * leftTabStop = [[[NSTextTab alloc] initWithType:NSLeftTabStopType location:85] autorelease];
	
	[paraStyle setTabStops:[NSArray arrayWithObjects:rightTabStop, leftTabStop, nil]];
	[paraStyle setHeadIndent:85];
	[paraStyle setAlignment:NSJustifiedTextAlignment];
	//[paraStyle setLineBreakMode:NSLineBreakByTruncatingTail];
	
	return [NSDictionary dictionaryWithObjectsAndKeys:
		paraStyle,NSParagraphStyleAttributeName,
		@"",kTRIngredientAttributeName,
		nil];
}


Does anyone know how to fix this?

Thanks

