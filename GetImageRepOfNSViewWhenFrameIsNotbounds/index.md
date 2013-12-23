---
layout: page
title: GetImageRepOfNSViewWhenFrameIsNotbounds
---

Howdy,
	I want to obtain an NSImage (or other image data suitable for posting to the pasteboard) of the contents of a subclass of NSView, and include transparency.  The caveat is that the contents of the NSView are drawn by NSBezierPath objects (not some existing image), and the frame of the view does not equal its bounds (so that to the user the contents appear scaled or translated).  Ultimately I would like for the resultant image to include an intersection of the path's bounds and the view's bounds, so space is not wasted, but out-of-bounds paths are not drawn.  This final step, however, seems trivial once I can obtain an image of the view in the bounds coordinates.

	I have tried creating a bitmap Image rep, locking focus to it and calling the drawRect method of my view, but the NSBezierPath objects always consider the origin of the image rep to be the point (0,0) not the origin of the bounds.  The image never appears scaled or translated.

	I have tried locking focus on the view and initializing an image rep with the view using initWithFocusedView:, but of course there is no transparency.  What ever appears in my window gets copied in, including things that can be seen "through" or "beside" my view.

	I have tried creating an image rep using "bitmapImageRepForCachingDisplayInRect:" and then "cacheDisplayinRect:toBitmapImageRep:".  The resulting image does include transparency, and the translation and size of the image are changed, but are incorrect.  So incorrect, in fact, that I have been unable to distinguish after more than 35 attempts at changing the rect passed in what's going on!  I have tried setting the rect's origin to zero, setting the rect's origin to negative, dividing the origin by 2, performing similar operations to the rep creation method, etc...  I am no longer making headway.  The problem seems to be that the paths in the resulting image are always translated by twice their intended amount, but changing the origin of the rect passed in has no effect!

I'm building for Mac OS X 10.4 (but would like to be 10.3 compatible) with XCode 2.4.
-Ben Spratling

----

If you get a PDF of a view using     -  (void)writePDFInsideRect:(NSRect)aRect toPasteboard:(NSPasteboard *)pboard it honours your bounds origin, transparency, etc. So perhaps you could convert from a PDF to a bitmap? Another way would be to create a temporary view that renders the same contents but without scaling - this is the same technique often used to implement printing. The user never sees it onscreen, but the rendering is captured by a graphics context (which in this case you would set up to be a bitmap image rep). Just ideas, haven't tried this myself. --GrahamCox

----

Thank you very much.  It is true, the      [self writePDFInsideRect:[self bounds] toPasteboard:[...]] does the best of any method so far, but it does not properly handle scale.  The paths appear the right size when the bounds are scaled, but the extent of the image is much too large when the scale is down, and much too small when the scale is up.

When I create a hybird rect that has the origin of the bounds and the size of the frame, the image is now the correct size, the paths are the correct size and place, but do not fill the image as they do in the view, they are cropped at the frame size, but the frame size in the bounds coordinates.


--Ben

----

So what about my further suggestion, or creating a temporary view that handles this? If your content is separate from the first view, this should be easy. In other words, if you've followed the model-view-controller paradigm and not stuffed your model into the view itself, it should be possible to render the model from any view. When implementing printing, it's very common to create a second view to handle the rendering to the printer. So think about this problem as like 'printing' your view content to a bitmap image.

If you really wish to stick to using the same view, it sounds to me like it's not bounds that causing you grief, it's the frame.Try setting the frame to equal the bounds... you may also need to use     -scaleUnitSquareToSize: to ensure that scaling is temporarily set to be 1.0.

----

Creating another view in another window has proved difficult.  So far the resulting image is blank.

All this worry over a missing     - (NSImage *)image from the NSView implementation.
--Ben

----

What about taking the PDF data (dataWithPDFInsideRect:), putting it in an NSPDFImageRep, and adding that to an NSImage whose size equals the size of the view? Might that work?

----

Bless you, that worked!
For those who come after, here's the code that makes it work:

    - (void)copy:(id)sender
{
       NSData *pdfData = [self dataWithPDFInsideRect:[self bounds]];
	NSPDFImageRep *pdfRep = [NSPDFImageRep imageRepWithData:pdfData];
	NSImage *anImage = [[NSImage alloc] initWithSize:[self frame].size];
	[anImage addRepresentation:pdfRep];
	[[NSPasteboard generalPasteboard] declareTypes:[NSArray arrayWithObject:NSTIFFPboardType] owner:self];
	[[NSPasteboard generalPasteboard] setData:[anImage TIFFRepresentation] forType:NSTIFFPboardType];
}


This code places a exact image of what the view generates including transparency, scaling and translation onto the pasteboard.  It is properly recognized by programs like Keynote 2 and TextEdit.  In programs like MS Word 2004 and Adobe Photoshop CS 1, the transparent areas appear black.  Of course, this is not suitable if your drawn image includes black.  But the Ink Window built-in since OS 10.2 does not yield transparent images anyway, and that's the funtionality I'm trying to extend.  All I need to do is add an optional transparency button for those who want that as a "power feature,"  or as we may now be calling them a "pro feature."

Thank again for your help on this problem.

Later today, when my internet connection gets faster, I'll upload the latest free beta version to
http://web.mac.com/bspratling/iWeb/Site/Ink%20Jot.html

--Ben

