---
layout: page
title: NSImageWithColorProfile
---



I have a source NSImage, with a color profile (sRGB, for example). I want to create a new image based on the source one, with the same color profile and colors.

With the example below, the destination image is created OK, P<nowiki/>hotoShop even reports the source and dest image have the same profile, but the colors in the destination are not the same as the source's color.

I'm pretty sure the colors in the destination image are the colors from the source, interpreted using my monitor profile.
If I dont specify a profile before writing the destination image, the profile reported by P<nowiki/>hotoShop is my monitor profile. Applying my monitor profile to the source image in P<nowiki/>hotoShop gives me the destination image.
Specifying the color profile of the destination image just changes the name displayed by P<nowiki/>hotoShop. The colors dont change.

I've Googled, searched CocoaBuilder.com, looked at tons of sample code, read the documentation on NSBitmapImageRep, NSImage, etc..., but I still dont have a clue on how to do it.

Any help would be greatly appreciated.

StephanBurlot

----

    
  NSData              *colorSyncProfile = nil;
  NSRect              srcRect;
  NSImage             *dstImage;
  NSBitmapImageRep    *dstImageRep;

  NSImage *srcImage = [[NSImage alloc] initWithContentsOfFile:SRC];
  NSBitmapImageRep *theBitmap= [NSBitmapImageRep imageRepWithData:[srcImage TIFFRepresentation]];

  if (theBitmap != nil)
  {
    // keep a copy of the current color profile
    colorSyncProfile = theBitmap valueForProperty:[[NSImageColorSyncProfileData] copy];
    // size the image at 72 dpi
    srcRect = NSMakeRect(0, 0, [theBitmap pixelsWide], [theBitmap pixelsHigh]);
    [srcImage setSize:srcRect.size];
  }
  else
  {
    NSLog(@"image has no bitmaprep");
    return;
  }

  // allocate destination image
  dstImage = [[NSImage alloc] initWithSize:srcRect.size];
  if (dstImage == NULL)
  {
    NSLog(@"dstImage is NULL!");
    return;
  }

  dstImageRep  = [[NSBitmapImageRep alloc] 
                  initWithBitmapDataPlanes:NULL
                  pixelsWide:srcRect.size.width 
                  pixelsHigh:srcRect.size.height
                  bitsPerSample:[theBitmap bitsPerSample]
                  samplesPerPixel:[theBitmap samplesPerPixel]
                  hasAlpha:[theBitmap hasAlpha]
                  isPlanar:NO
                  colorSpaceName:NSCalibratedRGBColorSpace
                  bytesPerRow:nil 
                  bitsPerPixel:nil];

  if (dstImageRep == NULL)
  {
    NSLog(@"dstImageRep is NULL!");
    return;
  }

  // set the color profile of the new bitmap
  [dstImageRep setProperty:NSImageColorSyncProfileData withValue:colorSyncProfile];
  [dstImage addRepresentation:dstImageRep];
  [dstImageRep release];

  // draw the src image into the destination
  [dstImage setBackgroundColor:[NSColor whiteColor]];
  [dstImage lockFocus];
  NSEraseRect(srcRect);
  [srcImage drawInRect:srcRect fromRect:srcRect operation:NSCompositeCopy fraction:1.0];
  [dstImage unlockFocus];
  [srcImage release];

  // fetch an NSBitmapImageRep (we cant save an NSCachedBitmap)
  [dstImage lockFocus];
  theBitmap = [[NSBitmapImageRep alloc] initWithFocusedViewRect:srcRect];
  [dstImage unlockFocus];

  // set the color profile of the destination
  [theBitmap setProperty:NSImageColorSyncProfileData withValue:colorSyncProfile];

  // and write a jpeg
  NSData *bitmapData = [(NSBitmapImageRep *)theBitmap representationUsingType: NSJPEGFileType
            properties:[NSDictionary dictionaryWithObject:[NSDecimalNumber numberWithFloat:0.8] 
                                                   forKey:NSImageCompressionFactor]];
  [bitmapData writeToFile:DST atomically:NO];

