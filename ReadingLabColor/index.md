---
layout: page
title: ReadingLabColor
---


Is it possible to sample a color in, for example, a tiff file, and get it's Lab value?
Specifically, I have an Lab tiff that contains an array of color patches.  What I would like to be able to do is read in the tiff and obtain the L*, a* and b* values of each of the color squares.
I can't see how to do that with the NSColor methods I'm familiar with.  I'm sure this is possible but I'm equally sure that I don't know how to do it.  Can anyone shed any light for me?

    targetImage is an     NSImage.  I find that when I open an 8 bit Lab tiff (with the following code) the program exits (with the following log entry).

    
- (void)didEnd: (NSOpenPanel *)sheet
	returnCode: (int)code
   contextInfo: (void *)contextInfo
{
	NSString *filename;
	
	if (code == NSOKButton) {
		NSLog(@"%@", [sheet filename]);
		[targetImage initWithContentsOfFile: [sheet filename]];
		NSLog(@"representations: %@", [targetImage representations]);
		NSLog(@"targetImage = %@", targetImage);
		if ([targetImage isValid]) {
			NSLog(@"%@ is valid", [sheet filename]);
		}
	}

    
2006-06-04 05:32:13.440 LabTargetReader[1970] myOpen
2006-06-04 05:32:27.526 LabTargetReader[1970] /Users/samlowry/Desktop/DJ130 profile testing/ECI2002CMYK i1(A3)_1_2 LAB.tif
2006-06-04 05:32:28.018 LabTargetReader[1970] Bad colorspace name NSCustomColorSpace
2006-06-04 05:32:28.018 LabTargetReader[1970] Bad colorspace name NSCustomColorSpace
2006-06-04 05:32:28.018 LabTargetReader[1970] Image 0 has invalid parameters

LabTargetReader has exited with status -1.


----

From that code alone, you're using NSImage completely wrong. It's failing because your targetImage isn't being initalized right. Try this instead:
    
targetImage = [[NSImage alloc] initWithContentsOfFile: [sheet filename]];


----
Thanks for your response.
    targetImage has been allocated earlier, in my     awakeFromNib method.  Is that not a good practice?
In any case, it works just fine for RGB images.

My chief concern, however, is how to then convert the RGB data to Lab.  Anyone have any insight?

----
Alloc/init must *always* be chained as above. It is acceptable to do the equivalent on separate lines, such as:

    
image = [NSImage alloc];
image = [image init...];


But there is no point to this and nobody does it. Widely separating your alloc/init is a bad idea, and failing to assign the value of init to the variable is an error.
----

So, is there no way to open a file in Lab color and sample it?
I can now open an RGB file, scan it and sample the color squares.  To get Lab values do I have to go through the Colorsync carbon APIs to make the conversion?

Is anyone here familiar with this sort of thing?

Thank you.

