---
layout: page
title: ImageAsTextViewBackground
---



I need some help, I want to make a NSTextView that has a picture as its background, and when you resize the view, then picture is also resized (just like in Terminal).
I tried to subclass NSTextView and override     drawViewBackgroundInRect:, but with no luck. Somehow picture appears in the view at 4 times smaller size, and it's not resized.

    
- (void)drawViewBackgroundInRect:(NSRect)rect
{
	[super drawViewBackgroundInRect:rect];
	NSString *path = [NSString stringWithString:@"somepicture.jpg"];
	NSImage *backgroundImage = [[NSImage alloc] initWithContentsOfFile:path];
	[backgroundImage drawInRect:NSMakeRect(0, 0, 300, 500)
				     fromRect:NSMakeRect(0, 0, 300, 500)
                                     operation:NSCompositeCopy fraction:1.0];	
}


(300 * 500 is a size of my image.)
if i change last method to this:     [backgroundImage drawInRect:rect fromRect:NSMakeRect(0, 0, 300, 500) operation:NSCompositeCopy fraction:1.0];, then the picture is resized with the view, but it still 4 times smaller than original and when text is being edited a white background appears over an image.
Image is ok, it happens with any other picture also.

I'm a newbie, so maybe I just do it in totally wrong&stupid way, I just need a direction where to dig...

----

Perhaps make the background invisible and use an NSImageView behind it.

*Yeah, that's how I would have done it, but you can read my argument...ahem, **discussion** in TransparentNSTextViewBackground about this very issue. It turns out this is a bad idea, even though it might work, so try not to do it (I've started trying myself). That said, I have no idea what the "right" answer is. --JediKnil*

