---
layout: page
title: QCViewToNSImageProblem
---

There hasn't been very much mention of Quartz Composer or QCView and its related materials on CocoaDev, but I'll ask the question anyway:
When I try using any of the various methods of grabbing an NSImage from an NSView, it doesn't work - instead, I just get a solid white image. Of course, changing the aspect ratio of the view changes the image's shape, but it's still white when I ask it to spit out into an image well. Here's the code:

    
- (NSImage *)imageFromView
{
	NSRect viewBounds = [mainScreen bounds];
	NSImage * image = [[NSImage alloc] initWithSize:viewBounds.size];
	[image setFlipped:YES];
	[image lockFocus];
	NSEraseRect(viewBounds);
	[mainScreen drawRect: viewBounds];
	[image unlockFocus];
	return [image autorelease];
}


Anyone have any idea? What gives?

-- JasonTerhorst

----
The short answer is that the technique you want to use to grab an image from a view is not one of the techniques that works.

The long answer is because calling -drawRect: directly has unpredictable results.  Your source code should call -display or a variant.  -display configures the current context for drawing the view, calculates the rect to send to -drawRect:, and calls -drawRect: or a -display variant for every nested view within the view being displayed.  There used to be a very prominent warning in Apple's documentation that you should not call -drawRect: directly.

The even longer answer is because  Quartz Composer and Core Imaging cache the pixels that are draw on the graphics card as an optimization.  When you ask  a view containing Quartz Composer generated images to draw without having properly configured its context, it is no wonder the view doesn't know how to draw and/or can't access the pixels.  I think you can capture the output from Quartz Composer in an image using QCView's -valueForOutputKey: and specifying the right key.

A slow but functional way to capture the output is NSView's -dataWithPDFInsideRect: sent to a QCView.


The way I capture images from QCRenderer in real-time is as follows:

You will need a QCRenderer, an NSOpenGLPixelBuffer, and an NSOpenGLContext.

Set up the NSOpenGLContext as specified in Apple's documentation.
Create the QCRenderer instance with the NSOpenGLContext made with the NSOpenGLPixelBuffer.
Remember to call QCRenderer 's -renderAtTime:arguments: before you try to grab an image.
The NSOpenGLPixelBuffer now contains the pixel data rendered by the QCRenderer.

Then do the part I skip:

Create an NSImage (NSBitmapImageRep) from the pixel data in the NSOpenGLPixelBuffer which is basically tho oposite of the common Apple examples where an OpenGL texture is created from an NSImage.

----

Should I be doing anything special (subclassing, etc), or can this all be done in one blocked method, like above, and simply creating new objects of each? This isn't working for me. I didn't have a QCRenderer before, so I don't understand how it attaches to the QCView, which already has the file loaded... The slower technique you mentioned doesn't even work.

----
You are correct.  I just tested and niether of these two approaches works:

    
- (IBAction)grabImage:(id)sender
{
   NSData      *imageData = [composerView dataWithPDFInsideRect:[composerView bounds]];
   [imageView setImage:[[[NSImage alloc] initWithData:imageData] autorelease]];
}


    
- (IBAction)grabImage:(id)sender
{
   NSRect      composerRect = [composerView bounds];
   composerRect = [composerView convertRect:composerRect toView:nil];
   NSData      *imageData = composerView window] dataWithPDFInsideRect:composerRect];
   [imageView setImage:[[[[[NSImage alloc] initWithData:imageData] autorelease]];
}


Have a look at Apple's QuartzComposerTexture sample.  This shows you how to create an OpenGL texture from a QCRenderer as described above.
I suspect that you will have to create your NSImage from the texture data.

----

Is there another way that doesn't involve such an expensive method as taking the long path through the land of OpenGL? I'm grabbing thumbnails to cache as NSImage instances, but I need it to be quick and simple, as my current UI will change that view and render the thumbnail every time the user makes a change in the panel. I have a feeling that OpenGL might not do it, or I'll have to sell my organs to do it. It appears that QCView is the strangest class ever, and is kind of frustrating. I can see why it isn't given much attention on CocoaDev. No one wants to.

*How is OpenGL expensive? For most tasks, if OpenGL is capable of doing it, then OpenGL is going to be the fastest way to do it, since it's massively hardware-accelerated on most machines. In fact, it's likely that the whole problem is because QC is using OpenGL to display stuff on-screen. NSOpenGLView has the same difficulties with extracting image data as you're seeing here. This would seem to indicate that using OpenGL to extract it is the right way to go.*

----

I found an example that goes in the right direction, I think. I'll play with it, and see if I can get it to work. It uses QCRenderer, but I've since switched to that from the QCView, because it's much more useful for my stuff. example on Apple's site: http://developer.apple.com/samplecode/QuartzComposerOffline/listing1.html

