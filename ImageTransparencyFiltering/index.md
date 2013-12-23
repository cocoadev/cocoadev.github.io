---
layout: page
title: ImageTransparencyFiltering
---

In the project I'm working on, I have to filter an image and change some colors around, then display it in a window. Problem is, the source images I use all have white backgrounds, which are somewhat annoying against the aqua-pinstripe background of the window. Basically, I'm looking for a way to make all the white pixels in an NSImage(Rep) transparent. So far I've had no success trying to access the alpha channel while munging through the image, even though NSImageRep has a -setAlpha: method. I have the feeling I'm missing something painfully obvious. Any suggestions? 
----
The description for setAlpha: is 

"- (void)setAlpha:(BOOL)flag

Informs the receiver whether the image has an alpha component. flag should be YES if it does, and NO if it doesn't."
Doesn't sound like you will be able to **add** an alpha channel to an image rep that way. I think you'll have to create a new image rep with alpha channel and copy the contents over. --AndreasMayer

----

Yes. More specifically, get an NSBitmapImageRep describing your image. Create a new image of the appropriate size, etc. Walk the raw bytes of your input imageRep one pixel at a time; if encounter an all white pixel (0xFF, 0xFF, 0xFF, 0xFF) zero the alpha as you copy (0xFF, 0xFF, 0xFF, 0x0). This technique works great for other kinds of image processing as well, such as RGB filtering, edge detection, pixelization, and so on. -- MikeTrent

----

That worked, thanks!

----

How does your filter work? Some code, please.

It's very simple, really. And rather messy.

    
-(void) processImageWithColours: (ColourPalette *)palette
{
    NSBitmapImageRep *bitmap, *new_bitmap;
    unsigned char *bytes, *newbytes;
    NSDictionary *swapcolour;
    int i, j, row, col, x, y, cell_width, cell_height;

    if(pose > 32)
        row = 0;
    else if(pose > 16)
        row = 1;
    else
        row = 2;
    col = (pose % 16) - 1;
    if(col == -1)
        col = 15;
    cell_width = ([source size].width / 16);
    cell_height = ([source size].height / 3);
    x = (col * cell_width);
    y = (row * cell_height);
    
    [image release];
    image = [[NSImage alloc] initWithSize: NSMakeSize(cell_width, cell_height)];
    [image lockFocus];
    [source compositeToPoint: NSMakePoint(0, 0)
                    fromRect: NSMakeRect(x, y, cell_width, cell_height)
                   operation: NSCompositeCopy];
    [image unlockFocus];
    
    bitmap = [[[NSBitmapImageRep alloc] initWithData: [image TIFFRepresentation]] autorelease];
    bytes = [bitmap bitmapData];
    new_bitmap = [[[NSBitmapImageRep alloc] initWithBitmapDataPlanes: NULL
                                                         pixelsWide: cell_width
                                                         pixelsHigh: cell_height
                                                      bitsPerSample: 8
                                                    samplesPerPixel: 4
                                                           hasAlpha: YES
                                                           isPlanar: NO
                                                     colorSpaceName: NSCalibratedRGBColorSpace
                                                        bytesPerRow: 0
                                                       bitsPerPixel: 0] autorelease];
    newbytes = [new_bitmap bitmapData];
    j = 0;
    for (i = 0; i < cell_width * cell_height * 3; i += 3) {
        unsigned char r, g, b;

        r = *(bytes + i);
        g = *(bytes + i + 1);
        b = *(bytes + i + 2);
        
        if(r == 255 && g == 255 && b == 255){
            *(newbytes + j + 0) = 255;
            *(newbytes + j + 1) = 255;
            *(newbytes + j + 2) = 255;
            *(newbytes + j + 3) = 0;
        } else {
            swapcolour = [palette swapColoursForR: r G: g B: b];
            if(swapcolour != nil) {
                *(newbytes + j) = swapcolour objectForKey: @"R"] intValue];
                *(newbytes + j + 1) = [[swapcolour objectForKey: @"G"] intValue];
                *(newbytes + j + 2) = [[swapcolour objectForKey: @"B"] intValue];
                *(newbytes + j + 3) = 255;
            } else {
                *(newbytes + j) = r;
                *(newbytes + j + 1) = g;
                *(newbytes + j + 2) = b;
                *(newbytes + j + 3) = 255;
            }
        }
        j += 4;
    }
    [image lockFocus];
    [new_bitmap draw];
    [image unlockFocus];
    [self setNeedsDisplay: YES];
}


It takes a cell from a sheet of sprites (no more than 34x34 in size, for 99.9999% of all possible future cases) and swaps the colours according to a pre-arranged pallette. It doesn't need to be particularly fast due to the rather small chunks it deals with, but compatibility with old machines is a must.

----

'nother question. Is there any painless way to turn a 32bpp image into a 16bpp image, or am I going to have to do that manually as well? I have a lot of values hard-coded for the 0-255 range, and just dividing them by 16 doesn't seem to help (even if I use a logical OR to try to access the high/low bitfields, the filtering doesn't get through when displayed at 16bpp). Problem is, while my app runs fine for users with Millions of Colours, those using Thousands just see the basic, unfiltered image. I could just extend the friendly middle finger to them and tell them to upgrade their graphics cards, but I'd rather be accomodating if it's at all feasible.

----

There are a couple of ways to solve this kind of problem. One way is to  you write seperate filters for each supported pixel format. The advantage of this method is you can hand tune your object code to be screaming fast; the disadvantage is you've got to maintain a bunch of separate filters, or do a bunch of pre-processor tricks, or use C++. Another way is to write filters that perform operations on arbitrary vaues (say, floats between 0 and 1) and write custom code to get and set pixels in each specific pixel format. The advantage of this method is all of your filter logic is in one place and can be applied to any pixel format; the disadvantage is you may pay a price in terms of performance or quality.

I'm telling you this because if you're going t write your own filters at all you need to cover all of the cases your filter will be used. That means you need to handle a variety of pixel formats. Or get that middle finger ready ...

Your specific problem, I suspect, may be that you're not interpreting the 16 bit video stream properly. Sometimes 16 bit ARGB is 1555 (i-bit alpha, 5-bit red, etc.) and sometimes it isn't. get the pixel format from your image rep and check its definition carefully.

Finally, there are ways to convert image reps to other formats (at least, I think there are) but you'll be better off coding your filter appropriately, as described above.

-- [[MikeTrent

----

I think you may be somewhat overestimating the requirements of my project. As long as it works, speed isn't terribly important, because most of the actual work is trivial (see code/comments above). The filter doesn't need to be particularly robust or extensible as long as it's accurate (very easy) and works (not so easy), since the problem space is extremely static (such that, were I so inclined, I could save .png images of every theoretically possible result in under 10 MB).

----

Well, I'm not sure why the code you posted wouldn't work with thousands of colors. You said the filtered image just doesn't show up? Maybe *image* contains more than one representation at the end of your filter method. Try replacing it instead of just drawing over the existing image. I.e. instead of

    
    [image lockFocus];
    [new_bitmap draw];
    [image unlockFocus];


try something like this:

    
   NSEnumerator *enumerator = [image representations];
   id rep;
   while (rep = [enumerator nextObject]) {
      [image removeRepresentation:rep];
   }
   [image addRepresentation:new_bitmap];


Also,

    
 bitmap = [[[NSBitmapImageRep alloc] initWithData: [image TIFFRepresentation]] autorelease];


why are you creating a new image rep here? Can't you just grab the existing representation? --AndreasMayer

----

With the code above (thanks!) I can play with the colors and transparency of an image. But the image itself is quite distorted. I only need it for a few images, I don't even need to see it onscreen. If it can read and write to a file it's fine. Here is how I use it.
    	
	int i, j, cell_width, cell_height;
	
	//old image
	NSImage *image =[[NSImage alloc] initWithContentsOfFile:@"testIN.pdf"];
       NSBitmapImageRep *bitmap = [[[NSBitmapImageRep alloc] initWithData: [image TIFFRepresentation]] autorelease];
	unsigned char *bytes = [bitmap bitmapData];
	//new image
	[view1 setImage:image];
	cell_width = [bitmap pixelsWide];
	cell_height = [bitmap pixelsHigh];
	NSImage *newImage = [[NSImage alloc] initWithSize:NSMakeSize(cell_width,cell_height)];
		
	NSBitmapImageRep *new_bitmap = [[[NSBitmapImageRep alloc] initWithBitmapDataPlanes: NULL
											pixelsWide: cell_width
											pixelsHigh: cell_height
										 bitsPerSample: 8
									   samplesPerPixel: 4
										  hasAlpha: YES
										  isPlanar: NO
								    colorSpaceName: NSCalibratedRGBColorSpace
									   bytesPerRow: 0
									    bitsPerPixel: 0] autorelease];
	unsigned char *newbytes = [new_bitmap bitmapData];
       j = 0;
    for (i = 0; i < cell_width * cell_height * 3; i += 3) {
             unsigned char r, g, b;
		r = *(bytes + i);
              g = *(bytes + i + 1);
              b = *(bytes + i + 2);
		
        if(r == 255 && g == 255 && b == 255){
            *(newbytes + j + 0) = 0;
            *(newbytes + j + 1) = 0;
            *(newbytes + j + 2) = 0;
            *(newbytes + j + 3) = 0;
        } else {
            *(newbytes + j + 0) = 0;
            *(newbytes + j + 1) = 0;
            *(newbytes + j + 2) = 0;
            *(newbytes + j + 3) = 255;			
        }
        j += 4;
    }
    [newImage lockFocus];
    [new_bitmap draw];
    [newImage unlockFocus];

    NSData *data = [newImage TIFFRepresentation];
    [data writeToFile: @"testOut.tiff" atomically: NO];
 

