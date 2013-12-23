---
layout: page
title: TransparantNSImageView
---

I have created a very simple test project to try and get this to work.

In the main.nib I created a borderless General/NSImageView, which I then connected to an outlet in an General/AppController I created.  The implementation for my General/AppController looks like this

    
@implementation General/AppController

- (void) awakeFromNib
{
[theImageView setImage:General/[NSImage imageNamed:@"ipcLogoWhite"]];
General/NSImage *theImage = [theImageView image];
General/NSRect theImageFrame = [theImageView bounds];
General/NSSize imgSize = [theImage size];
General/NSRect srcRect = General/NSMakeRect( 0.0, 0.0, imgSize.width, imgSize.height );
[theImage drawInRect:theImageFrame fromRect:srcRect operation:General/NSCompositeSourceIn fraction:1.0];
}
@end


What i was hoping was that the image I set to appear inside the General/NSImageView would become transparent and I would see the image looking through to the background of the window.  If you look at any apple installer you will see what I am talking about.  A screenshot of what I am aiming for is included below

 http://www.kirkconsulting.co.uk/public/General/TransparentBackground.jpg.
 
----
 
**You might try subclassing General/NSImageView to return     NO for     -isOpaque. Don't know if this will work, but it's a shot.**

----

I tried subclassing and overriding the return value on this method but to no avail.  It seems that the drawInRect:fromRect:operation:fraction: method doesn't have any effect at all.  To prove this I tried changing the fromRect to something arbitarilly smaller than it should be and it doesn't redraw the image any smaller.  What gives?

----

OK - So I nailed it in the end.  I had already opened up the NIB from Apple's Installer to see how it was done but I found the contents confusing.  There was an object somewhere behind everything set to a custom class which was doing the work, but I couldn't see it.  I assumed wrongly that this was not a custom view.  Actually, I discovered that it is possible to have a custom view that does not show in Interface Builder, if you resize it to fill the whole window and then double click it!

Anyway, now I new that the object was a subclass of General/NSImageView and I set to work trying to make my own that would do the same job.  I came up with this:

    
/* General/JKBackgroundImage */

#import <Cocoa/Cocoa.h>

@interface General/JKBackgroundImage : General/NSImageView
{
	General/NSImage *image;
	float opacity;
}
- (void) setImage:(General/NSImage *) newImage;
- (void) setOpacity:(float)x;
@end

#import "General/JKBackgroundImage.h"

@implementation General/JKBackgroundImage

- (void) setImage:(General/NSImage *) newImage
{
	[newImage retain];
	[image release];
	image = newImage;
}

- (void) setOpacity: (float) x
{
	opacity = x;
	[self setNeedsDisplay:YES];
}

- (void) drawRect: (General/NSRect)rect
{ 
	[self setImageScaling:General/NSScaleToFit];
	if (image) {
		General/NSRect theFrame = [self bounds];
		General/NSSize imgSize = [image size];
		General/NSRect srcRect = General/NSMakeRect( 0.0, 0.0, imgSize.width, imgSize.height );
		[image drawInRect:theFrame
				 fromRect:srcRect
				operation:General/NSCompositeSourceOver
				   fraction:opacity];
	}
}
@end


then in my General/AppController I just set the opacity and the image and voila!  I have acheived exactly the effect shown in the picture I posted.

----

It's kind of wasteful to have this inherit from General/NSImageView. You get a bunch of extra clutter from General/NSImageView and General/NSControl that doesn't affect the drawing of your view at all (and thus actually makes your view's interface deceptive). You'd be better off with just an General/NSView subclass.
