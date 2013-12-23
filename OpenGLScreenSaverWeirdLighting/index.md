---
layout: page
title: OpenGLScreenSaverWeirdLighting
---



OK, I've been posting a bit about this screensaver I'm doing, it's a flag saver, and right now I'm at the final stage. Right now, it's all fine, except at low resolutions some weird wavy lines show up all over it. Here's an example:

http://mac.halomods.com/images/FlagSaver.tiff

Any idea what might be causing it? Here's my lighting code:

    
   GLfloat LightPosition[4] = {2.0f,0.0f,-5.0f,1.0f};
   GLfloat LightDiffuse[4] = {1.0f,1.0f,1.0f,1.0f};
   GLfloat LightAmbient[4] = {0.5f,0.5f,0.5f,1.0f};
   GLfloat light0Specular[4] = {0.0f, 0.0f, 0.0f, 1.0f};
   GLfloat lightModelAmbient[4] = {0.0f,0.0f,0.0f,1.0f};
   GLfloat materialSpecular[4] = {0.0f, 0.0f, 0.0f, 0.0f};
   GLfloat materialEmission[4] = {0.0f, 0.0f, 0.0f, 0.0f};
   GLfloat materialAmbient[4] = {0.0f, 0.0f, 0.0f, 0.0f};
   GLfloat materialDiffuse[4] = {1.0f, 1.0f, 1.0f, 1.0f};
   glLightModelfv(GL_LIGHT_MODEL_AMBIENT, lightModelAmbient);
   glMaterialfv(GL_FRONT, GL_AMBIENT, materialAmbient);
   glMaterialfv(GL_FRONT, GL_DIFFUSE, materialDiffuse);
   glMaterialfv(GL_FRONT, GL_SPECULAR, materialSpecular);
   glMaterialfv(GL_FRONT, GL_EMISSION, materialEmission);
 
   glLightfv(GL_LIGHT1, GL_AMBIENT, LightAmbient); 
   glLightfv(GL_LIGHT1, GL_DIFFUSE, LightDiffuse);   
   glLightfv(GL_LIGHT1, GL_POSITION,LightPosition);   
   glLightfv(GL_LIGHT1, GL_SPECULAR, light0Specular);


----

Does the texture of the flag have a detailed "stitch" like pattern? Perhaps try another texture and see if it still appears. I don't think it's a lighting issue, but that's a complete guess. -- RyanBates

----

Yes, it turned out to be the texture. How do I go about correcting this? I really, really like this texture... I plan to make it a little brighter, or, rather, make the colors more rich. But other than that, how could I eliminate this stuff?

----

Well, you can play around with different OpenGL anti-aliasing techniques, but these may not fix the problem and will probably slow down the screen saver a lot. Instead, I suggest resizing the source texture to the size of the screen saver view (or a little bigger) before applying it to the flag OpenGL mesh. That should get rid of the wavy lines. I'm not sure what to do if that doesn't work. -- RyanBates

----

How exactly would I go about resizing the source texture? I took the NSBitmapImageRep and tried doing a setSize: on it, but that didn't do much. I'm new to using images in Cocoa - how would I do this? I've got a simple loadTexture method that I stole from the Cocoa conversion of the NeHe lesson 11, which takes an NSBitmapImageRep and converts the data to the right format.

----

JeffDisher provided a good example on how to do this at ThumbnailImages. I'll repeat it here so you don't have to wade through the mess:

    
@implementation NSImage (ResizeImage)

- (NSImage *)imageWithSize:(NSSize)newSize
{
        NSImage *newImage = [[NSImage alloc] initWithSize:newSize];
        NSRect oldRect = NSMakeRect(0.0, 0.0, [self size].width, [self size].height);
        NSRect newRect = NSMakeRect(0.0, 0.0, newSize.width, newSize.height);

        [newImage lockFocus];
        [self drawInRect:newRect fromRect:oldRect operation:NSCompositeCopy fraction:1.0];
        [newImage unlockFocus];
        return [newImage autorelease];
}

@end


Note, this is a category which extends the NSImage class. Your source needs to be an NSImage in order to get this to work. You might also need to set the interpolation to high before resizing (see the page for more details).

-- RyanBates

----

Well, for some reason, I'm just getting a blank texture. Here's the code I'm using, I combined code from ThumbnailImages and it seems as if it should work.

    
        NSImage *myImage = [[[NSImage alloc] initWithContentsOfFile:filename] autorelease];
	myImage = [myImage imageWithSize:NSMakeSize([self bounds].size.width, [self bounds].size.width)];
	
	NSData *tiff_data = [[[NSData alloc] initWithData:[myImage TIFFRepresentation]] autorelease];
	theImage = [[NSBitmapImageRep alloc] initWithData:tiff_data];


myimage should contain the resized image, and the other 2 lines should convert from NSImage to NSBitmapImageRep. What's wrong?

----

It's been a long time since I've worked with OpenGL, but does the size of the texture need to be divisible by some value? Try adding this code after resizing the image to write it to a file.

    
myImage [[TIFFRepresentation] writeToFile:@"~/test.tiff" atomically:YES];


Let me know if the file is black. -- RyanBates

----

I recently improved some older code I had to save opengl to tiff files in /tmp for later encoding to movies. The tiffs had to have a width that was a clean multiple of 4, or else they were bogus.

--ShamylZakariya

----

I did it, and it is creating a valid image, I had tested that before with the same method. This is what it looks like:

http://mac.halomods.com/images/BadFlagSaver.jpg

Maybe I should just save and load from file? I shouldn't have to, though...

[EDIT] i figured it out - I forgot it had to be in powers of 2, to use the basic GLLoadBitmap stuff. Got it, it looks great! Thanks![/EDIT]
-- BobInDaShadows

