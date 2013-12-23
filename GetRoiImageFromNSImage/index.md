---
layout: page
title: GetRoiImageFromNSImage
---



Hi, folks. The question is how to get some roi image, buffer, from NSImage. Simply i have that buffer (void* or char* for example ), but i need to draw a rectangle in it. Making it NSImage, setting fockus, drawing a rect and unsetting fockus i thing is good, but i want to get that buffer back to my char*... And don't know how ) 

thnks, sorry my english )

----
Create an NSBitmapImageRep with your buffer, then create an NSGraphicsContext with your NSBitmapImageRep. Set the context as current, then drawing commands will draw into your buffer.

What's a roi image?

Region Of Interest (ROI).

----
I mean raw image with some roi. Ok i thing that's work. Only one more question, how to draw rectangle ))

[NSBezierPath bezierPathWithRect:NSMakeRect(5, 5, 10, 10)] fill]; seems not "drawing" "rectangle" ) 

----
Did you set a color first? You should post more code. The code you posted there won't even compile.

----
    
unsigned char* data2=(unsigned char*)data; // where is "data" is my "raw image data, my buffer". it has 4 channels

NSBitmapImageRep* offscreen = [[NSBitmapImageRep alloc]
  initWithBitmapDataPlanes:&data2
  pixelsWide:320 
  pixelsHigh:240 
  bitsPerSample:8
  samplesPerPixel:4 
  hasAlpha:YES
  isPlanar:NO 
  colorSpaceName:NSDeviceRGBColorSpace
  bytesPerRow:0
  bitsPerPixel:0];
	 
[NSGraphicsContext saveGraphicsState];
[NSGraphicsContext setCurrentContext:[NSGraphicsContext 
  graphicsContextWithBitmapImageRep:offscreen]];
	
[[NSColor colorWithCalibratedWhite:1 alpha:1] set];
[[NSBezierPath bezierPathWithRect: NSMakeRect(100,100,130,130)] stroke]; // drawing a rectangle // have a problems here

[NSGraphicsContext restoreGraphicsState];

// here i have my "raw image data" with a rectangle in it


I can see that rectagle i need is drawed, a little invalid (i mean it is not rect but filled square - but i need a rectangle with only four lines). Very often have an error "CGSColorMaskCopyARGB8888" 

----
That code won't compile either. Are you copy/pasting directly from the code that you're running? It's already very difficult to debug other people's code, and it becomes impossible to debug code that's just a paraphrase of what's actually running.

----
It's copy/past. Why it won't compile? Seems i have no problems with it, only that error and throwing off my application (sometimes.. ok, frequently). Ofcause it's only part of sources (they are too huge), it's a cutted function... The question is why i have that error, what it meens and how to fight with it.

----
Now it should compile, since it's been fixed. Originally your "problem" line read     [[NSBezierPath bezierPathWithRect: NSMakeRect(100,100,130,130) stroke];, which has more opening than closing brackets and will give you a syntax error. It's difficult to know whether this is the only difference between what you've posted and what you're compiling. Hopefully now that you've fixed it, this is your actual code.

----
Yes ) i'v missed one bracket, my mistake, accidentlly pressed delete button ) But the problem still exists ) With app throwing off with "CGSColorMaskCopyARGB8888" error.

----
Is there another method to draw "one pixel" rectangle? 

----
NSRectFill(NSMakeRect(floor(x)+0.5, floor(y)+0.5, 1, 1)); - will draw one pixel, without anti-alasing.

----
I mean to draw rectangle with line's width of one pixel. Unladen one pixel :) Simply any other method to draw rectangle. Not from 'bezier' class, because it seems too slow drawing something.

