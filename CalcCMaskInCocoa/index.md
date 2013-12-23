---
layout: page
title: CalcCMaskInCocoa
---

I need to use the Quickdraw function CalcCMask in a Cocoa Project. This is because I have to mask NSImages so that they are drawn completely over the background pixels. I can get a CGImage from my NSImage and use either chromacolor masking or CGImageMaskCreate() but neither method for doing this in Quartz works if there are any white pixels in the image. Chromacolor masking can be used with the function CGImageCreateWithMaskingColors() with the key color as white to remove the whitespace between the image and its rectangle but if there are any white pixels in the image content the background will show through them. A similar problem exists if one creates a mask with CGImageMaskCreate(). The mask created with this method has the same bit depth as the CGImage and the background will show through depending on how dense the pixels in the source image were. Creating either a one bit source image or mask just increases the contrast to black and white but actually creates more white pixels in the mask/image. You can think of the problem like this: Image one is a black circle with a white center. Image two is a gray rectangle. You want to draw the black circle over the gray rectangle but you want the center of the circle to remain white. You can't do this with Quartz but you could do it with quickdraw using PicHandles and regions more or less like this:
    
GetPort(&savedPort);
if( (error = NewGWorld(&myOffscreenWorld, imageBitDepth, &rectYouWant, 0L, 0L, 0L)) != noErr)
	handleError();
SetPort((GrafPtr)myOffscreenWorld); //sourcemap for CopyMask pixmap
DrawPicture(picHandle, &rectYouWant);
SetPort(savedPort);
if( (error = NewGWorld(&myOffscreenBITMap, imageBitDepth, &rectYouWant, 0L, 0L, 0L)) != noErr)
	handleError();
myRGB = FillInColor(0, 0, 0); //white
LockPixels(myOffscreenWorld->portPixMap);
CalcCMask((BitMap *)&myOffscreenWorld->portPixMap, &(myOffscreenBITMap->portBits), &tempRect, &tempRect, &myRGB, NULL, 0);
CopyMask((BitMap *)&myOffscreenWorld->portPixMap, &(myOffscreenBITMap->portBits), &savedPort->portBits, &tempRect, &tempRect, destRect);
SetPort(savedPort);

One could do this in Cocoa using more or less the same code *IF* there was some way to get a PicHandle from an NSImage or a GCImage. Looking at these postings on CocoaDev:  

*SollidMaskwithCGImageMaskCreate  
*HowDoICreatePICTDataInCocoa  
*PicHandle  

it looks like everyone else is having as much trouble doing this as I am. The method posted using MovieImportComponent importer doesn't work for me either because GraphicsImportGetAsPicture() returns an error. The JBPICTCreator class is nice but it won't work for this problem because what I have to do is draw the PicHandle version of the NSImage and nobody seems to know how to get one. Using potrace as Graham Cox suggests seems like a huge hassle to get arround a missing graphics technology in Quartz when it should be possible to get a PicHandle in Cocoa. Any help you can give me would be appreciated.

----

Using the clipboard trick given in HowDoICreatePICTDataInCocoa gets you an NSData object containing PICT data. Converting this to a handle is easy using Carbon. I think the function you need is PtrToHand (from memory, so may be wrong - but it's one of those). http://developer.apple.com/documentation/mac/Memory/Memory-104.html  --GC

----
Here is a simple method for creating PICTs I invented that seems to work well:
    
void NSImageToPict(NSImage *theImage, PicHandle* thePict, NSRect drawingRect)
{
OpenCPicParams picParams;
Rect tempRect;
	
tempRect.top = 0;  // haven't yet figured out how to cast an NSRect to a Rect so:
tempRect.left = 0;
tempRect.right = drawingRect.size.width;
tempRect.bottom = drawingRect.size.height;
tempRect = *(Rect*)&drawingRect;
picParams.srcRect = tempRect;
picParams.hRes = 0x00480000;
picParams.vRes = 0x00480000;
picParams.version = -2;
*thePict = OpenCPicture(&picParams);
[theImage drawInRect: drawingRect fromRect: NSZeroRect operation: NSCompositeSourceOver fraction: 1.0]; //1.0 equals opaque
ClosePicture();
}

Now I'm working on making my program draw masked images using CalcCMask as in the above example and it looks like it will work.

----

What's all that voodoo with tempRect? You set up its fields one by one then overwrite it using a cast which is wrong. Given that an NSRect consists of an origin and a size which are all 32-bit float values, it cannot be directly cast to a QD Rect structure, which uses 16-bit integer fields. So the line      tempRect = *(Rect*)&drawingRect is incorrect and likely to cause things to go badly wrong. Also, if this actually works it's presumably because somewhere deep down in its implementation drawing an NSImage calls QuickDraw's CopyBits function. Is that something you can really rely on? Only certain QD calls can be captured by an open picture, and it seems odd that NSImage would be using a QD call. That's not to say it doesn't, but it's not documented as such. It certainly wouldn't work with all of Cocoa's transfer modes because they are not the same as QDs transfer modes (so I'd also suggest that using NSCompositeSourceCopy mode would be marginally safer) The clipboard trick (write as TIFF, read as PICT representation) is much more reliable because Apple will see to it that it continues to work as documented. --GC


----
The voodoo method that I used above has problems so I've used a variation on the clipboard trick. The PICT data's picFrame is random so I set it to the same rect as the NSRect. Also the last line "[pboard releaseGlobally];" won't compile so I assume that you meant [pb releaseGlobally];
    
void NSImageToPict(NSImage *theImage, PicHandle* thePict, NSRect drawingRect) //- (NSData *)pictDataForImage:(NSImage *)img
{
	NSPasteboard *pb = [NSPasteboard pasteboardWithName:@"essimagecategory"];
	[pb types];
	[pb declareTypes:[NSArray arrayWithObject:NSTIFFPboardType] owner:nil];
	[pb setData:[theImage TIFFRepresentation] forType:NSTIFFPboardType];
	NSData *pictData = pb dataForType:[[NSPICTPboardType] retain];
	PtrToHand(pictData, (Handle*)thePict, [pictData length]);
	//Pict's Rect is random so set it to the drawing Rect:
	(*(**thePict)).picFrame.top = 0;
	(*(**thePict)).picFrame.left = 0;
	(*(**thePict)).picFrame.right = drawingRect.size.width;
	(*(**thePict)).picFrame.bottom = drawingRect.size.height;
	[pb releaseGlobally];
}

The picHandle I get appears to have valid data in it but I haven't been able to get it to draw in the Cocoa view subclass that calls this function. Does this look right? What I'm doing is setting some variables in my view subclass and then sending it a message setNeedsDisplay: YES. This causes Cocoa to send it the message - drawRect: (NSRect)rect. At this point I want to make the QuickDraw calls I need to implement masked drawing instead of drawing the image using Cocoa but no QuickDraw functions will draw into my view. My assumption is that when Cocoa calls drawRect, the port is properly set, so what am I missing?

----

It looks like a better approach, but has some problems. You are using the pointer to the NSData object as if it were the pointer to the data itself. That's incorrect. Instead use NSData's      - bytes method and pass that to PtrToHand. I think you should then find that the picFrame field is correct and should not need to be set explicitly. To draw the PICT, you can either use an NSQuickDrawView, or set up the appropriate context (off the top of my head I'm not sure what's involved with that). -GC

----
Just to be a little more explicit, the assumption that anything QuickDraw-related is set up in an NSView's     -drawRect: method is wrong in general. It is true for NSQuickDrawView but all other views do not interact with QD at all.

----

Yep, I can't find any information about doing this any other way. What's not clear is whether you can mix Quartz and QD calls within NSQuickDrawView, but as far as I can tell you can, since it inherits from NSView and only provides one method      - qdPort. --GC
----
QuickDraw and PICTs? Have you considered getting away from legacy technologies?  Perhaps the problem is the framework you're using.

----

I'd concur with that - except that the function CalcCMask has no exact equivalent in Cocoa (think of the "Instant Alpha" feature in the latest iLife/iWork apps - clearly it's possible but at least for now private to Apple). Even so I don't think the approach of using a PICT is really a good one - while it's an easy path to take it's not a good direction to be going at this point. I think the way to go would be to use the      - CGImageCreateWithMaskingColors function to make a mask image with holes, then use a marching-squares algorithm to find the path of the outer edge of the mask and use that to "fill in" any interior holes. Without being familiar with the new matte feature in iWork it sounds like this is what it does.  --GC
----
I am sure I just don't understand the requirements, but why not just use standard NSImage compositing with alpha channel?  Why doesn't the image you want to composite have alpha channel already ?  Why not just use Photoshop or your favorite image editing software to add alpha channel to whatever ridiculous images don't already have it ?  I say ridiculous because seriously, refusing to work with alpha channel makes you a dinosaur.  Alpha compositing has been mainstream for 15 to 20 years now.  How old are these images ?

If the images are programmatically generated, generate them with alpha channel.  If the images are captured from a video camera or something, chroma key might be the right approach, but if so, did you use a blue screen or green screen ?

I just can't help thinking that attempting to use PICT handles and Quick Draw for anything in 2007 is laughably stupid.
If you really want to programatically create "masks" i.e. an impoverished alpha channel, this article might lead the way:
http://www.losingfight.com/blog/2007/08/28/how-to-implement-a-magic-wand-tool/

----

I don't know what the original poster's reasoning for wanting this is, but there are plenty of times when none of the suggestions you're making are possible. For example, take a digital picture from a camera, but oh - you want to knock out the background to include an object in a word processing document and run text around its edge. Hrmmm... is it reasonable to have to buy, install and learn Photoshop just for this? Would you ship an app with system requirements that include "requires additional application to enable feature X that costs ten times what this does"? No, and that's why iWork '08 has "instant alpha". Using chromakey to create an alpha channel is readily available, but it's not quite the same - if something in the middle of your foreground object happens to have a background colour, it gets knocked out too. What's needed is a flood fill from outside of the foreground object which defines the "background" area, then inverted to form a clipping mask. There is no function in Cocoa that does this, but there is in QuickDraw - CalcCMask. It's this lack of exact replacement for some of QuickDraw that leads some to take the QD route, even now. I don't advocate it, but I can see the reason for it. That article is indeed a very good starting point for a better approach that is more appropriate for Cocoa - in fact I'm thinking I might work up a little NSImage category for replacing CalcCMask since this is the third time in about as many months I've seen people asking about it. --GC

(Later update:) I had a little play with Andy Finell's code as linked above. It does in fact do 99.5% of what this problem requires. In his case, he uses the generated mask to "select" the pixels using a marching ants effect. I hacked the code so that instead it used the generated mask to mask the original image when displaying it. This is easily done using the Quartz function     CGCreateImageWithMask. I also changed the mask generation so it generated the inverse mask, but this was just a case of filling it with FF instead of 0. The result is exactly the same as what CalcCMask does - it knocks out the background but doesn't knock out interior holes that match the background. Thus the linked code is very suitable for this task - and has plenty of room for optimisation too (on a 1.25 GHz G4 it takes a short but perceptible time to do its thing).The changes I needed to make to use it for masking were very minimal. The main part was writing some code to turn an NSBitmapImageRep into a CGImageRef, which was just down to the way Andy had  set up his demo app, and not relevant to the masking itself. In other words it's a suitable solution not involving QD.

One other comment regarding masking versus alpha. There's no difference, it's just that alpha can have values other than on or off. If you want to replicate CalcCMask exactly, then you would use an on/off mask, but in fact you'd definitely get better results by anti-aliasing the edges of the mask. Andy's code doesn't attempt this, so it really is very similar to CalcCMask - however, the mask detection/creation routines could be modified to create a soft edge to the mask which would be great for matteing photographs. I may have a go at doing that. --GC
----
Thanks for your help. I'm moving an application to Cocoa and I'm almost done but I can't finish the job because the old program made heavy use of CalcCMask to create bitmap masks for images so they could be clipped to their outline and drawn over other images without the background showing through. As you note, with Quartz this is impossible. It's impossible to mask an image so that the background won't show through. Chromacolor masking won't work because just as you say any pixels in the image that have the same value as the key color won't be masked. It is also impossible to find the outline of the content pixels in a image like CalcCMask did. Of course I can open all of the images in the application's resources folder and edit them so that the parts that need to be opaque and transparent are correct, and then just draw them. But this a huge job and will only solve the problem for images loaded from the application's resources. This is why I came up with the "ridiculous, laughably stupid" idea of using the QuickDraw code in the Cocoa app. It seems ironic that to implement image masking in Quartz the images have to be created masked correctly with a program like Photoshop before Quartz can draw them since even idiots like me could mask them easily with a few ancient severely deprecated Quickdraw calls. The one thing in Quartz that SUX is image masking.

----

I've put my modified version of Andy Finell's code on my site, URL is http://apptree.net/code/MagicWandGCMod1.zip It still needs more work to be an exact replacement for CalcCMask however - at present it's more like a SeedCFill followed by a CopyDeepMask. It does show the approach is likely to bear fruit though. If I get any time I'll have a go at making a CalcCMask replacement, which is very similar but flood fills from all four edges of a given rectangle, not just from a single point. I've kept Andy's demo project more or less intact with minor alterations to display the masked image instead of the wagic wand selection of the original. The additional code is in an NSImage category, and makes use of one class from Andy - SelectionBuilder. The only mod I made to that class was a flag to return a normal or inverted mask. To demo the results, open an image and click somewhere. The clicked area is masked out. You can keep clicking to remove more and more from the image. --GC
----
I attempted to see if images with transparent areas in my application's resources would solve my image masking problem. The answer is no. I created some tiff images with alpha and erased the area between the image rectangle and content so it was transparent. Then I loaded them and drew them in my view subclass. The transparent boundary areas will be transparent *ONLY IF* I draw them into a rect that is the same size as the image rect. If I scale them by passing a destRect that is a different size than the srcRect in [NSImage drawInRect:...] the transparent areas will be rendered as opaque white. Interestingly this is only true for drawing to the screen. If I print or save myDocument it will be rendered correctly. I also converted them to CGImages before drawing and the same is true for them.

----
Well then you're doing something wrong. Images with alpha will render correctly no matter what scaling is applied.
----
No, this is probably a bug in the Cocoa NSImage class. I saved the test image in gif and png and if I load it from the app resources from either of these file types and scale it the transparent parts render correctly. Theoretically NSImage knows about all sorts of file formats and loads and displays them correctly but in this case it doesn't. I've read all sorts of files into NSImages and NSImage draws them right - except for the transparent parts of tiff. This looks like a big bug since tiff is the basic image format in Cocoa just like pict was in Classic.

----
The fact that it would be such a big bug is exactly why I say that you're doing something wrong. I've been working with Cocoa since before 10.0 and I've never even *heard* of such a problem before, much less encountered it, and I've had plenty of opportunity to do so. I reaffirm my claim that you're doing something wrong. Perhaps you should PostYourCode.
----
I concur that the OP is doing something wrong.  How are the tiffs produced ?  Maybe the tool used saves tiff incorrectly?

----

Also, what composition mode are you using with     [NSImage drawInRect...]. The right one to use generally to get correct transparency rendering is NSCompositeSourceOver --GC

