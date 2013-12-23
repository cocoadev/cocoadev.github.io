---
layout: page
title: MakingFractalsUsingBitmapImageRep
---

**NOTE: this posting needs a little organization that it does not presently have. I will get to work organizing it once I have time, but I'm a little busy as of now. -- RobRix**

Dennis C. De Mars answered a question on one of the development lists (either AppleComputer's cocoadev list or TheOmniGroup's macosx-dev list), and I followed up on it. He kindly provided some example code, as well as some general comments about the code. It may well be useful to you if you're interested in fractals, or if you want to know how to draw into an NSImage one pixel at a time (did anybody say image filters?).

The comments he sent me are as follows:

*
Here's the source code. All of the code that is relevant to the use of NSBitmapImage is in the method "generate" in MyImageController.m. I don't know how much context should be included with the example, so I included the entire file along with the header file. I also include WindowSizing.m which contains the definition of one small method used in MyImageController.

A rudimentary Mandelbrot set generator can be built from the included code with a few steps in Interface Builder. One would have to create a window with an NSImage inside it, and then put a scroller around the NSImage. Then the custom class MyImageController should be created with one outlet called imageView and one action called DoGenerate. An instance of MyImageController should be created. The imageView outlet should be connected to the NSImage that was placed in the window. A menu item called "Generate" should be created and connected to MyImageController, invoking the action "DoGenerate." That's all there is to it.

A few things would have to be added to make an actual useful fractal generator. At the very minimum a way to zoom into the fractal should be added. That wouldn't be very hard. Then a dialog box to change a few things like the image size and escape count, and a way to save the fractal image as a graphics file would be about all that would be required for a rudimentary Mandelbrot set generator.
*

And now for the code, straight from the files:

**MyImageController.h**

    
#import <Cocoa/Cocoa.h>

@interface MyImageController : NSObject
{
    IBOutlet	NSImageView*	imageView;
    
                NSSize		imageSize;
}

- (void) generateImage;

- (void) DoGenerate: (id) theObject;

@end


**MyImageController.m**

    
#import "MyImageController.h"
#import "WindowSizing.h"

static int
InSet(int i, int j);

struct PixelColors
{
    unsigned char red;
    unsigned char blue;
    unsigned char green;
    unsigned char alpha;
};

// Constants

const int imageWidthInPixels	= 200;
const int imageHeightInPixels	= 200;
const NSRect imageRectInComplexPlane = { {-2.0, -2.0}, {4.0,4.0} };
const int maxCount = 200;
const struct PixelColors blackPixelColor = {0x00, 0x00, 0x00, 0xff}; 
const struct PixelColors toyFractalColors[6] 
    = {	{0xff, 0xff, 0x00, 0xff}, {0xff, 0x00, 0xff, 0xff}, {0x00, 0xff, 0xff, 0xff}, 
        {0x00, 0xff, 0x00, 0xff}, {0xff, 0x00, 0x00, 0xff}, {0x00, 0x00, 0xff, 0xff} };
        
// Class implementation

@implementation MyImageController

- (void) DoGenerate: (id) theObject
{
    NSSize theSize;
    
    [self generateImage];
    // Adjust window
    theSize = [NSScrollView frameSizeForContentSize:	imageView image] size]
                            hasHorizontalScroller:	YES
                            hasVerticalScroller:	YES
                            borderType:			[[NSBezelBorder
                            ];
    imageView window] setMaxContentSize: theSize];
}

- (void) generateImage
{
    [[NSBitmapImageRep* bitmap;
    unsigned char* pixels;
    int i,j;
    NSImage* theImage;
    struct PixelColors fractColor;
    
    // We allocate the NSBitmapImageRep instance and initialize it.
    // The initialization method has a lot of parameters because it is very versatile.
    // It can handle many different arrangements of pixel data.
    //
    // We pick a pretty vanilla organization that probably corresponds to the most
    // common case. Each pixel is a 32 bit (four byte) value that contains four components:
    // red, blue, green, alpha (alpha is the "coverage" component, it determines 
    // the transparency of the pixel). Each component is eight bits (one byte).
    //
    // Finally, we let the NSBitmapImageRep instance allocate a buffer of the proper size to handle
    // our image; alternatively, you could allocate your own buffer for the pixel data and pass the
    // address to NSBitmapImageRep when initializing.
    
    bitmap = [[NSBitmapImageRep alloc]
                initWithBitmapDataPlanes:	NULL	// Let the class allocate it
                pixelsWide:			imageWidthInPixels
                pixelsHigh:			imageHeightInPixels
                bitsPerSample:			8	// Each component is 8 bits (one byte)
                samplesPerPixel:		4	// Number of components (R, G, B, alpha)
                hasAlpha:			YES
                isPlanar:			NO
                colorSpaceName:			NSDeviceRGBColorSpace
                bytesPerRow:			0	// 0 means: Let the class figure it out
                bitsPerPixel:			0	// 0 means: Let the class figure it out
                ];
                
    // Now that we have created the NSBitmapImageRep instance, we will ask it for a pointer to the
    // buffer it created so we can write into it (this would be unnecessary if we had allocated our
    // own buffer). We store the pointer in the variable "pixels".
    
    pixels = [bitmap bitmapData];
    
    // In the following loop, i is the horizontal coordinate of the pixel, and
    // j is the vertical component.
    // i loops over columns, j loops over rows
    
    for(j= 0; j < imageHeightInPixels; j++)
    {
        for (i = 0; i < imageWidthInPixels; i++)
        {
            int dwell = InSet(i,j);
            if (dwell < 0)	// then point i,j is in Mandelbrot set
                fractColor = blackPixelColor;
            else
                fractColor = toyFractalColors[dwell % 6];
            
            *pixels++ = fractColor.red;
            *pixels++ = fractColor.blue;
            *pixels++ = fractColor.green;
            *pixels++ = fractColor.alpha;

        }
    }
    
    // In our NIB file, we've created an NSImageView instance in a window and connected it
    // to an outlet in this class called "imageView"
    /// So, now we create an NSImage... 
    theImage = [[NSImage alloc] initWithSize: imageSize];
    // ...place our NSBitmapImageRep in the NSImag ...
    [theImage addRepresentation: bitmap];
    // ...and place the NSImage in the NSImageView.
    [imageView setImage: theImage];
}

- (id) init
{
    [super init];
    imageSize.width = imageWidthInPixels;
    imageSize.height = imageHeightInPixels;
    return self;
}

@end

int
InSet(int i, int j)
{
    double x, y, re, im;
    int count;
    NSPoint imageOrigin = imageRectInComplexPlane.origin;
    NSSize imageSize = imageRectInComplexPlane.size;
    
    // Convert to floating point
    x = imageOrigin.x + (i*imageSize.width)/imageWidthInPixels;
    y = imageOrigin.y + (j*imageSize.height)/imageHeightInPixels;
    re = 0;
    im = 0;
    
    for(count = 0; count < maxCount; count++)
    {
        double reTemp, imTemp;
        reTemp = re*re - im*im + x;
        imTemp = 2.0*re*im + y;
        if ((reTemp*reTemp + imTemp*imTemp) >= 4.0)
            break;
        else
        {
            re = reTemp;
            im = imTemp;
        }
    }
    
    if (count == maxCount)
        return -1;
    else
        return count;
    
}


**WindowSizing.h**

    
//
//  WindowSizing.h
//  ToyFractal
//
//  Created by demars on Sat May 05 2001.
//  Copyright (c) 2001 Dennis C. De Mars. All rights reserved.
//

#import <Cocoa/Cocoa.h>

@interface NSWindow (WindowSizing) 

    - (void) setMaxContentSize: (NSSize) contentSize;

@end


**WindowSizing.m**

    
//
//  WindowSizing.m
//  ToyFractal
//
//  Created by demars on Sat May 05 2001.
//  Copyright (c) 2001 Dennis C. De Mars. All rights reserved.
//

#import "WindowSizing.h"


@implementation NSWindow (WindowSizing)

    - (void) setMaxContentSize: (NSSize) contentSize
    {
        NSRect contentRect = { {0.0, 0.0}, {0.0, 0.0} }, frameRect;
        
        contentRect.size = contentSize;
        frameRect = [NSWindow frameRectForContentRect:contentRect styleMask:[self styleMask]];

        [self setMaxSize: frameRect.size];
    }

@end




-- Dennis C. De Mars, edited and posted by RobRix

----

One thing though, I think you'll want to [bitmap release] after [theImage addRepresentation: bitmap];  This fixed a memory leak for me.

-JustinGarcia

----

Shouldn't theImage = [[NSImage alloc] init] be autoreleased before returning from the method -generateImage?
-Mike Anen

----

I found a major error, not in memory management, but with finding the actual set! This line:
    
if ((reTemp*reTemp + imTemp*imTemp) >= 2.0)

in the InSet method, is wrong. It should be 4.0 instead of 2.0. The author was avoiding the square root operation that you normally do when finding the modulus of a complex number, but forgot to square the other side. Because it makes the set come out wrong, I've gone ahead and fixed it in the original code listing. --OwenYamauchi

*If I had Dennis' e-mail address, I'd notify him... I don't, so I'll just say thanks Owen, Mike, Justin. I can't say much about the actual code unfortunately. -- RobRix*

----

Another mistake. This one isn't crippling, but if you try to implement another color scheme it screws things up. In these lines:
    
*pixels++ = fractColor.red;
*pixels++ = fractColor.blue; *//should be green*
*pixels++ = fractColor.green; *//should be blue*
*pixels++ = fractColor.alpha;

blue and green are in the wrong order; switch them around. --OwenYamauchi

----

I think it would be easier if you wrote your fractal code using an image unit, because image units already work on a per pixel basis, so you only need to write the code to generate a single pixel based on its coordinates, as core image handles applying the 'filter' (although it is a filter with no input, making it a generator) to the image. I think that this would be easier, but I could be wrong.

