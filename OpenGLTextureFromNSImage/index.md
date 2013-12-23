---
layout: page
title: OpenGLTextureFromNSImage
---



Is there any sample code demonstrating on how to load NSImage into OpenGL texture?

----

Check out Apple's Alphabet ScreenSaver example: http://developer.apple.com/samplecode/Sample_Code/Graphics_3D/Alphabet.htm. In particular, look at AlphabetLetter.m ( http://developer.apple.com/samplecode/Sample_Code/Graphics_3D/Alphabet/AlphabetLetter.m.htm ). The relevant section is:

    
        // prepare bitmap image rep

(I've hacked the first few lines up to make it more general -- MikeTrent)
    
        bigRect.origin = NSZeroPoint;
        bigRect.size = [image size];
        [image lockFocus];
        upsideDown = [[NSBitmapImageRep alloc] initWithFocusedViewRect: bigRect];
        [image unlockFocus];
        [image release];
        
        // since OpenGL draws images upside down, we need to flip this image along 
        // the y axis. I know a cool trick for doing this when texture mapping, 
        // but I want something a little more general for now
        bytesPerRow = [upsideDown bytesPerRow];
        bitsPerPixel = [upsideDown bitsPerPixel];
        hasAlpha = [upsideDown hasAlpha];
        height = (int)bigRect.size.height;
        _imageRep = [[NSBitmapImageRep alloc] 
            initWithBitmapDataPlanes:NULL
            pixelsWide:(int)bigRect.size.width
            pixelsHigh:height
            bitsPerSample:[upsideDown bitsPerSample]
            samplesPerPixel:[upsideDown samplesPerPixel]
            hasAlpha:hasAlpha
            isPlanar:[upsideDown isPlanar]
            colorSpaceName:NSCalibratedRGBColorSpace
            bytesPerRow:bytesPerRow
            bitsPerPixel:bitsPerPixel];
        from = [upsideDown bitmapData];
        to = [_imageRep bitmapData];
        for (i = 0; i < height; i++) {
            bcopy((from + bytesPerRow * i), (to + bytesPerRow * (height - i - 1)), bytesPerRow);
        }
        [upsideDown release];
        
        // create the texture
        if (useTextures) {
            GLenum format;

            format = hasAlpha ? GL_RGBA : GL_RGB;
            glGenTextures(1, &_tex);
            glBindTexture(GL_TEXTURE_2D, _tex);
            CheckGLError("glBindTexture");
            glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP);
            glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP);
            glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
            glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
            CheckGLError("glTexParameteri");
            glPixelStorei(GL_UNPACK_ALIGNMENT, 1);
            glPixelStorei(GL_UNPACK_ROW_LENGTH, bytesPerRow / (bitsPerPixel >> 3));
            CheckGLError("glPixelStorei");
            glTexImage2D(GL_TEXTURE_2D, 
                0, 
                GL_RGBA, 
                bigRect.size.width, 
                bigRect.size.height, 
                0, 
                format, 
                GL_UNSIGNED_BYTE, 
                to);
            CheckGLError("glTexImage2D");
        }


Note at the end of this wild ride, the texture _tex has been initialized with the contents of "image". Now if *I* were writing this code, I'd have created an NSImage category or a function that did all this in one simple place. But that's sample code for you. If you clean this up into a function/category, please repost your example.

-- MikeTrent

----

This code doesn't work for me. I guess I have some problems with my code elsewhere, because the only texture I can load is from one of the Nehe tutorials (NeHe.bmp file). I've also used code from those tutorials but still no luck.

----

Fish CheckGLError out of the Alphabet example and see if maybe one of your OpenGL calls is failing. Don't forget GL textures generally need to be aligned to powers of 2. There are many ways to deal with this problem, including resizing images, using subtextures, or using new OpenGL extensions. I don't have an example of any of these things handy...

----

Thanks. Is there any difference if I am loading tiff or jpg or any other files?

----

Nope, tiff and jpg aren't problems, because they're all turned into bitmapped data in the end by NSBitmapImageRep.

I had entirely forgotten about power of two textures though... What you need to do if you don't have power of 2 textures is turn on the rectangle extension -- that is to say, replace all instances of GL_TEXTURE_2D with GL_TEXTURE_RECTANGLE_EXT in MikeTrent's code above. Also keep in mind that rectangular textures aren't treated like square power of 2 ones. Power of 2 textures are referenced relative to the image, while rectangular ones are referred to by actual pixels. So a regular 128 x 128 texture's extreme corners would be  (-1, -1) and (1, 1), while a rectangular 400 x 300 would be (0,0) and (400, 300). Important to keep in mind when applying them to geometry. As an aside, GL_TEXTURE_RECTANGLE_EXT is actually recommended by Apple as one of the more productive speed optimizations you can make to GL code.

----

Do you have any links, or can you just explain briefly, why GL_TEXTURE_RECTANGLE_EXT is a productive speed optimization? I've searched all over and can't find any explanation. I'm curious as to why something that seems like it would end up being slower than GL_TEXTURE_2D, due to the fact that it has to be more flexible, would actually turn out to be faster.

----

Correct me if I'm wrong, but I don't think GL_TEXTURE_RECTANGLE_EXT works with e.g. Rage 128 systems, only Radeons. A significant number of OS X-using Macs have Rage 128's... and can't upgrade. (Think iMac G3, PowerBook G3, et cetera.)

----

Humh, yes. That's what I've heard too. Won't there be any problems with this?

Also could you please tell me how to clear all stuff I've drawed. In my drawRect method i am doing something like this:
    
if (visible)
{
//draw my cube
}
else
{
//if not visible clear out stuff

[[NSColor clearColor] set];
NSRectFill([self bounds]);
glClearColor( 0.0f, 0.0f, 0.0f, 0.0f );
  glClear( GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT );
//should this help? it doesn't work :(
}


----

First off, when you're using OpenGL, don't use Cocoa drawing, use OpenGL drawing. Second, +clearColor is just that-- not the colour you clear to, but a clear colour, that is, a colour with no alpha. You're basically filling a rect with air with the NSRectFill call.

Third, the glClear call looks right, but you need to change two things with the overall structure here and one with the glClearColor call. Unlike NSColor's +clearColor, glClearColor refers to the background colour-- the colour that glClear reverts to. So instead of filling it with 0,0,0,0, fill it with 0,0,0,1 (black) or 1,1,1,1 (white) or some other colour-- just make sure that the fourth value, the alpha transparency, is set to 1.

As for the overall structure, the first thing is that you don't want to call glClearColor every frame unless you want to actually change the background colour every frame. I'd recommend doing it somewhere like in -initWithFrame: or even -awakefromNib.

The more important thing here is that clearing stuff doesn't just happen when you're not drawing the same thing again. You want to clear every frame, straight off, and then either draw or don't draw the cube based on whether or not visible is YES. As an example:

    
-(void)awakeFromNib
{
    glClearColor(0.0, 0.0, 0.0, 1.0);
}

-(void)drawRect:(NSRect)frame
{
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    ...
    if(visible)
    {
        // draw that cube
    }
}


Now I will suggest you find a copy of TheOpenGLRedBook [http://fly.cc.fer.hr/~unreal/theredbook/] and read at least the first few chapters. You really need to have a better idea of the structure of things here.

----

I am really new to this OpenGL stuff, so thanks for the link!

----
Rather than copying the bitmap data rows to flip the image use...
    
[image setFlipped:YES];

to flip it before -initWithFocusedViewRect.

Kelvin Nishikawa

----

I am an OpenGL noob, so I don't know but this worked for me:

    
-(void) genImage {
	NSString *path = @"/Users/g4b3/Pictures/RandomWeb/24.226.81.95--photogallery--Aerials--IM001108.JPG";
	
	NSBitmapImageRep *bitmap = [NSBitmapImageRep imageRepWithContentsOfFile:path];
	texBounds = NSMakeRect(0, 0, [bitmap size].width, [bitmap size].height);
	
	GLenum format = [bitmap hasAlpha] ? GL_RGBA : GL_RGB;

	glGenTextures (1, &texName);
	glBindTexture (GL_TEXTURE_RECTANGLE_EXT, texName);
	glTexImage2D (GL_TEXTURE_RECTANGLE_EXT, 0, format, texBounds.size.width, texBounds.size.height, 0, format, GL_UNSIGNED_BYTE, [bitmap bitmapData]);
}


Gabriel Handford

----

Where is http://developer.apple.com/samplecode/Sample_Code/Graphics_3D/Alphabet.htm ? 

When I go there, it just redirects to Apple's 404 page.  It really annoys me that you can't just view a

big list of  **all ** the sample code examples.  Or better yet,

Apple could just use their FTP server for the code itself, and have the HTML pages index into that.

As of 10-13-04 you can see all the OpenGL sample code at http://developer.apple.com/samplecode/GraphicsImaging/idxOpenGL-date.html but the Alphabet sample is not there any more. 

----

Question for someone knowledgable: I have been working with various examples on the web to get reliable loading of NSBitmapImageRep into a texture (including the above examples) and have been quite successful.  My app downloads images from the web, however, and would always find oddball images with strange color profiles, bit depths, etc.  There is a line in one of the above examples that solved my display problems, but I have no idea why :-)

What does the math calculation in this line do?

glPixelStorei(GL_UNPACK_ROW_LENGTH, bytesPerRow / (bitsPerPixel >> 3));

Thanks for any explanation!

----

1.     (bitsPerPixel>>3) converts the number of bits per pixel into the number of bytes per pixel (there are eight bits to a byte, and shifting right by three is the same as dividing by two three times, i.e. eight (2^3).

2.     bytesPerRow/(bitsPerPixel>>3) therefore calculates the width of the texture in pixels... also known as the "row length", as you can see from the constant     GL_UNPACK_ROW_LENGTH.

----

Sometimes the NSBitmapImageRep may have padding at the end of each line.  Setting the GL_UNPACK_ROW_LENGTH tells OpenGL to skip these pad pixels.  GL_UNPACK_ALIGNMENT is similar, if you don't set that OpenGL will align each row to the next boundary (default is 4 bytes).  When you work with RGB data that is packed RGBRGBRGB, you have to set this to 1 tell OpenGL that the data is packed.  The default of 4 will work fine with RGBA or ARGB data.

----

Hey, anyone knows how to get floating point data to opengl for textures? I'm kind of confused, it doesn't seem to be possible using NSImage/NSBitmapImageRep. What would I use then?

----

Another option to a flipped image is to just flip the glTexCoord2f coordinates.

