---
layout: page
title: OpeningAnImageFromAFile
---



I am trying to open an image from a file using a standard open dialog and using the resulting path in an -initWithContentsOfFile method.  When I test the NSImage that I just created with -isValid I get 'NO'.

What might I be doing wrong?
----

One thing you're doing wrong is asking for help with your code without providing the code. ;-)
----
Fair point.  I guess I just thought I might be missing something conceptually and someone might see that right off.  

Anyway, here is how I'm trying to open the image:
    
- (void)didEnd:(NSOpenPanel *)sheet
	returnCode:(int)code
   contextInfo:(void *)contextInfo
{
	NSData *data;
	
	if (code == NSOKButton) {
		NSLog(@"ok button was pressed!");
		NSLog(@"%@", [sheet filename]);
		[theImage initWithContentsOfFile: [sheet filename]];
		if ([theImage isValid]) NSLog(@"the image is valid!");
		if (![theImage isValid]) NSLog(@"the image is NOT valid!");
		NSLog(@"representations: %@", [theImage representations]);
		
		[gangView setMyImage: theImage];
	}
}


Thanks.

*Yup, you do have a conceptual problem. In ObjectiveC, you need to     alloc an image before you initiate it. The proper way to create your image is:*
    
theImage = [[NSImage alloc] initWithContentsOfFile:[sheet filename]];

*On the offchance that you already called     alloc (slightly bad style), at least remember that     init methods can return a different object than the one you have, so you still need to use "    theImage = " --JediKnil*
----
That was it.  Thanks.

