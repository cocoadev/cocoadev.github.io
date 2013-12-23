---
layout: page
title: UsingAlphaInImage
---

This little example should copy my image, which is a clear image with a rectangle, to my window. The background of the window is gray and the image consists of a red rectangle.
The horizontal lines are copied like I would expect but the vertical lines are not drawn or a white bar is added.
If I understand things correct, General/NSCompositeSourceOver should copy my image over my gray background (like it does for the horizontal lines).
If you want to try things, simply create a cocoa application and put a custom view in the main window (make sure it is at least 200*200). 
The custom class is General/MyView. Simply add the code in a file that ends in .mm to avoid some errors.

What am I doing wrong?

    
#import <Cocoa/Cocoa.h>

@interface General/MyView : General/NSView
{
	float frameHeight;
	float frameWidth;
	General/NSBitmapImageRep *imageCache;
	General/NSImage *mImage;
	unsigned char *imageDataPtr;
	int imageRowBytes;
}
@end

@implementation General/MyView

//-----------
// initWithFrame
//-----------
- (id)initWithFrame:(General/NSRect)frameRect
{
	if ((self = [super initWithFrame:frameRect]) != nil) 
	{
		frameHeight=frameRect.size.height;
		frameWidth=frameRect.size.width;
		
			imageRowBytes = (((int)(frameWidth / 16) + 1) * 16) * 4;
			imageDataPtr = (unsigned char *)malloc(imageRowBytes * frameHeight);
			for (int i=0; i<imageRowBytes*frameHeight/4; i++) 
			{
				((unsigned int *)imageDataPtr)[i] = 0xffffff00;
			}
			imageCache = [
				General/[NSBitmapImageRep alloc] 
	    		initWithBitmapDataPlanes:&imageDataPtr
			pixelsWide:frameWidth
		      pixelsHigh:frameHeight
			bitsPerSample:8 
			samplesPerPixel:4	
			hasAlpha:YES 		
        		isPlanar:NO
			colorSpaceName:General/NSDeviceRGBColorSpace
			bytesPerRow:imageRowBytes bitsPerPixel:(8 * 4)
		];
	mImage=General/[[NSImage alloc]init];
	[mImage addRepresentation:imageCache];
	// draw a rect
	[self plotRect: 20 ypos:20 xpos2:150 ypos2:150 red:255 green:0 blue:0  alpha:0];

	}
	return self;
}

//-----------
// drawRect
//-----------
- (void)drawRect:(General/NSRect)rect
{
	General/[[NSGraphicsContext currentContext] setShouldAntialias:NO];
	// gray background
	General/[[NSColor grayColor]set];
	General/NSRectFill(rect);
	//image on top 
	[mImage compositeToPoint:General/NSZeroPoint operation:General/NSCompositeSourceOver ];
}

//-----------
// plotRect
//-----------
-(void) plotRect:(int) x1 ypos:(int) y1 xpos2:(int)x2 ypos2:(int) y2 red:(unsigned char)r green:(unsigned char)g blue:(unsigned char)b alpha:(unsigned char)a
{
	int x,y;
	unsigned char *ptr=imageDataPtr + (imageRowBytes*y1 + x1 * 4);
	unsigned char *ptr2=imageDataPtr + (imageRowBytes*y2 + x1 * 4);
	
	//plot horizontal line
	for(x=x1; x<=x2; x++)
	{
		*ptr++=r;	*ptr++=g;	*ptr++=b;*ptr++=255-a;
		*ptr2++=r;	*ptr2++=g;*ptr2++=b;*ptr2++=255-a;	
	}
	
	ptr=imageDataPtr + (imageRowBytes*y1 + x1 * 4);
	ptr2=imageDataPtr + (imageRowBytes*y1 + x2 * 4);

	//plot vertical line
	for (y=y1; y<=y2; y++)
	{
		*ptr=r; *(ptr+1)=g; *(ptr+2)=b; *(ptr+3)=255-a; 
		*ptr2=r; *(ptr2+1)=g; *(ptr2+2)=b; *(ptr+3)=255-a;
		ptr+=imageRowBytes; 
		ptr2+=imageRowBytes;
	}
	
}
@end


----
I doubt this is the root of your problem, but when you create a new General/NSImage you should never do a plain alloc/init. Instead, use     initWithSize: and pass it the size you want.

----
When working with General/NSBitmapImageRep and alpha you need to pre-multiply the non-alpha components by the alpha component.  Yes this is strange, but there you go.  If you do not alpha does not work properly and you can get strange results.
----
Yes, I was looking at that before but what I always forgot was to start by setting all pixels to 0x00000000 instead of 0xffffff00.
Thanks!
