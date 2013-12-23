---
layout: page
title: CIImageProviderUsingTiles
---

**UPDATE: I found the problem. Since size_t is unsigned, the value of "-x" is only valid for x = 0 (which corresponds to the left-most column).**

**So in the provideImageData method, the line:**

    CGPoint drawpoint = CGPointMake(-x,height+y-h);

**should be**

    CGPoint drawpoint = CGPointMake(-((float)x),(float)(height+y)-h);

**With this, the code seems to work perfectly. I'm leaving this as is, in case anyone else has the same problem.**

**The lesson here that you should always beware of types that wrap around primitives; you can't always tell if they're signed or not just by looking at them. With size_t, at least, it's only logical for it to be unsigned (what would a negative sign even mean). But you might not guess with something like clock_t. A few clicks on "Jump to Definition" could save you a lot of headaches. This is also good to remember whenever something only works when a value is equal to zero. **

I am trying to implement the CIImageProvider informal protocol to facilitate "lazy" conversion of a CGLayer into a CIImage by specifying a tile size.

I have created a factory class, CIL<nowiki/>ayerImage, which has the class method:
+(CIImage *) imageWithCGLayer:(CGLayerRef)layer calibrated:(BOOL)calibratedColorSpace;


This uses a private helper class, CIL<nowiki/>ayerProvider, which implements the CIImageProvider protocol to do the actual conversion. This class stores the layer and colorspace.

The relevant code from CIL<nowiki/>ayerImage is: 

    

+(CIImage *) imageWithCGLayer:(CGLayerRef)layer calibrated:(BOOL)calibratedColorSpace {
    CIImage * img = nil;

    CGColorSpaceRef cs;
    //set up colorspace
    if(calibratedColorSpace) {
        cs = CGColorSpaceCreateWithName(kCGColorSpaceUserRGB);
    } else {
        cs = CGColorSpaceCreateDeviceRGB();
    }
    
    //create a provider instance
    CILayerProvider * prov = [[CILayerProvider alloc] initWithLayer:layer colorspace:cs];
    
    if(prov) {
        CGSize size = CGLayerGetSize(layer);
        //Set up the CIImage to call the provider
        img = [CIImage imageWithImageProvider:prov size:size.width : size.height format:PIX_FORMAT colorSpace:cs
                                      options: [NSDictionary dictionaryWithObjectsAndKeys:
                                                [NSNumber numberWithInt:PROV_TILE_SIZE],  kCIImageProviderTileSize, nil]];

        [prov release];

    }
    
    if(cs)
        CGColorSpaceRelease(cs);
    return img;
}



And the relevant code from CIL<nowiki/>ayerProvider is:

    

- (void)provideImageData:(void *)data bytesPerRow:(size_t)rowbytes origin:(size_t) x :(size_t)y size:(size_t)width:(size_t)height userInfo (id)info {
    
    size_t len = height * rowbytes;
    void * newdata = malloc(len); //create temporary holder for image data
    
    if(newdata) {
        CGContextRef cx = CGBitmapContextCreate(newdata, width, height, 8, rowbytes, cs, kCGImageAlphaPremultipliedFirst);
        //get the offset for drawing the layer in this tile
        CGPoint drawpoint = CGPointMake(-x,height+y-h); /* h = height of the layer (set on init) */

        CGContextDrawLayerAtPoint(cx, drawpoint, layer);
        
        
        memcpy(data, newdata, len); //copy the image data into the data pointer provided
        free(newdata); //free the temporary data
        CGContextRelease(cx);
    }
}



This compiles without any warnings and doesn't log any errors at runtime.

The problem is that the resulting CIImage contains only the the tiles from the left-most column of the image. All other tiles are clear (as in black with alpha=0).

What I've done to explore this problem:

* NSLog() x and y in provideImageData: . The method does indeed get called for every expected coordinate.
* Fill every rectangle with red before drawing the layer. The fill actually *works*, but the layer is drawn, once again, only in the left-most column.
* Use CGContextDrawLayerInRect(0,0,width,height) to draw the entire layer into each tile. This works as advertised. The resulting image is a tiled pattern of the scaled-down layer.
* drawpoint = CGPointZero. Similar to above; correctly tiles the lower-left corner of the layer.
* Do the actual drawing in its own thread. I did this on a hunch. It acted exactly the same as the above code.
* CGBitmapContextCreate(data... instead of CGBitmapContextCreate(newdata... ; again, same behavior.
* Use an NSImage instead of a CGLayer. *Exactly* the same problem.
 

Especially in light of the fact that the problem still occurred with NSImage, I think I am either missing something fundamental, or this part of Core Image is completely broken. A Google search on this topic provided literally no help. Does anyone have any suggestions?

Failing that, does anyone know an efficient way to create a CIImage from a CGLayer that doesn't immediately copy the entire layer? Would it be possible to extend CIImage to achieve the same result?

