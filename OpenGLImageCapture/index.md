---
layout: page
title: OpenGLImageCapture
---



    
-(id)imageRepFromFrame:(NSRect)frame {
    BOOL hasAlpha;
    int i, ii, imagePixelCount;
    int imageWidth, imageHeight, imageX, imageY, bytesPerPixel;
    char cr, cg, cb, ca, cr2, cg2, ca2, cb2;
    char *firstByte;
    char alphaTop, alphaBottom;
    char *pointer, *stepPointer, *endPointer, *endStepPointer, *Red, *endRed;
    int halfHeight, timerLoop;
    long *topLong, *bottomLong, tempLong, topLong1, topLong2, bottomLong1, bottomLong2;
    int bytesPerRow;
    
    imageWidth=(int)frame.size.width;
    imageHeight=(int)frame.size.height;
    imageX=(int)frame.origin.x;
    imageY=(int)frame.origin.y;
    bytesPerPixel=4;

    bytesPerRow=imageWidth*bytesPerPixel;
    if (bytesPerPixel==3) hasAlpha=NO;
    else hasAlpha=YES;
    NSLog(@"x: %i y: %i w: %i h: %i bytesPerPixel: %i bytesPerRow: %i", imageX, imageY,imageWidth , imageHeight, bytesPerPixel,bytesPerRow);
    id bir=[[NSBitmapImageRep alloc] initWithBitmapDataPlanes:nil
                pixelsWide:imageWidth
                pixelsHigh:imageHeight
                bitsPerSample:8
                samplesPerPixel:bytesPerPixel
                hasAlpha:hasAlpha
                isPlanar:NO
                colorSpaceName:NSDeviceRGBColorSpace
                bytesPerRow:imageWidth*bytesPerPixel
                bitsPerPixel:8*bytesPerPixel];
    char *bytes=[bir bitmapData];
    firstByte=bytes;
    pointer=firstByte;

    glReadBuffer(GL_BACK);
    glReadPixels(imageX, imageY, imageWidth, imageHeight,GL_BGRA, GL_UNSIGNED_INT_8_8_8_8_REV, bytes);
    halfHeight=floor(imageHeight/2);
    endPointer=firstByte+imageHeight*imageWidth*4-bytesPerRow;
    imagePixelCount=imageHeight*imageWidth;
    topLong=(long *)firstByte;
    bottomLong=(long *)endPointer;
    for (timerLoop=0;timerLoop<1;timerLoop++) {
    topLong=(long *)firstByte;
    bottomLong=(long *)endPointer;
    for (i=0;i<halfHeight;i++) {
            for (ii=0;ii<imageWidth;ii++) {
           tempLong=*topLong;
            topLong1=*bottomLong<<8;
            *topLong=topLong1+((*bottomLong&0xFF000000)>>24);
            bottomLong1=tempLong<<8;
            *bottomLong=bottomLong1+((tempLong&0xFF000000)>>24);
            topLong++;
            bottomLong++;            
        }
        bottomLong=bottomLong-imageWidth-imageWidth;
    }
    }
    return bir;
}





OpenGLImageCapture is a method to draw a 3D OpenGL model into an offscreen buffer and then capture this drawing into an NSImage. This method is useful if you want to composite 3D drawings on top of existing Quarts 2D drawings.

more about this as soon as I get the hang of doing this --zootbobbalu

Hey Zootbobalu,

I've been looking for something like this. I want to take a screen snap of an OpenGL rendering. I have tried several ways with no luck. I have been able, on other platforms, to do this by rendering to memory. But I havn't figured it out on Mac.
I'm at:
jimd@surfnetusa.com

Are you subscribed to the Mac-OpenGL list at http://lists.apple.com/ ? There has been a lot of discussion about render-in-texture and getting pixels back from surfaces and contexts in general. I also recommend you check out AGL, as it's got some handy stuff. -- RobRix, a Cocoa veteran and OpenGL front-liner.

No I'm not subscribed to that list. I do have the 2002 WWDC DVD's, and there is a bunch of OpenGL stuff on them. I'm only doing this because I want to help the non-gamer. I'm planning on providing a simple script interpreter so the beginner Cocoa programmer can start playing with OpenGL. 

--zootbobbalu

Sounds like a good plan. I recommend subscribing to that list no matter what you're doing with OpenGL, though, they're incredibly helpful on issues of API, fast-lane, potential non-graphical uses for the GPU, and many other things. And I've learned quite a bit about high-level shading languages also.

Oh, and as I mentioned, AGL has a lot of goodness for Cocoa programmers. Like the font stuff! Apple's doing good stuff, from what I've seen. -- RobRix

