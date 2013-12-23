---
layout: page
title: UIImage
---



Part of the iPhone General/UIKit framework.

<code>+ (id)applicationImageNamed:(General/NSString*)name;</code>

Instatiates the image from the application directory.

<code>- (void)compositeToPoint:(General/CGPoint)p operation:(int)operation;</code>

Composites the image into the current drawing context. Is operation same as General/NSCompositingOperation?

<code>- (General/CGSize)size;</code>

Crashes.

----

I've been trying to find out how to draw an General/UIImage rotated 90�/-90�, but since you can't use (i think) General/AppKit you can't use whole of General/NSAffineTransform which makes it difficult. Any ideas on how to draw an General/UIImage rotated?

----

I think you should be able to just go down into General/CoreGraphics and do this: http://developer.apple.com/documentation/General/GraphicsImaging/Reference/General/CGAffineTransform/Reference/reference.html

Prolly not the answer you were looking for, but that's about the only thing I can come up with.

-- Jacob

----

The simplest method to rotate a General/UIImage is to use a General/CGAffineTransform and rotate its view. The nice thing is on the iPhone OS there are some convenience methods to do this, so the result looks something like this:
    
	General/CGAffineTransform rotate = General/CGAffineTransformMakeRotation(1.57079633);
	[myImageView setTransform:rotate];


The General/CGAffineTransformMakeRotation takes a float in radians, so this example will rotate the image 90&#730; clockwise.

-- Pelted

Better to use M_PI_2, no?
