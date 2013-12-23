---
layout: page
title: OpenGLTextureMapping
---

I'm using some code I got from Nehe tutorials

    
- (BOOL) loadGLTextures
{
   BOOL status = FALSE;

   if( [ self loadBitmap:[ NSString stringWithFormat:@"%@/%s",
                                    [ [ NSBundle mainBundle ] resourcePath ],
                                    "Photo 8.jpg" ] intoIndex:0 ] )
   {
      status = TRUE;

      glGenTextures( 1, &texture[ 0 ] );   // Create the texture

      // Typical texture generation using data from the bitmap
      glBindTexture( GL_TEXTURE_2D, texture[ 0 ] );

      glTexImage2D( GL_TEXTURE_2D, 0, 3, texSize[ 0 ].width,
                    texSize[ 0 ].height, 0, texFormat[ 0 ],
                    GL_UNSIGNED_BYTE, texBytes[ 0 ] );
      // Linear filtering
      glTexParameteri( GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR );
      glTexParameteri( GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR );

      free( texBytes[ 0 ] );
   }

   return status;
}


I'm compiling in Xcode 2.4

I get 2 warning and both say "MyOpenGLView may not respond to loadBitmap- intoIndex" so the Bitmap I put in never gets to the drawing and I get nothing but white on my shape.  Any suggestions?

----

So.... have you got a loadBitmap method in your object?

----

Yeah, sorry about that. 
Heres the code.  

    
- (BOOL) loadBitmap:(NSString *)filename intoIndex:(int)texIndex
{
   BOOL success = FALSE;
   NSBitmapImageRep *theImage;
   int bitsPPixel, bytesPRow;
   unsigned char *theImageData;
   int rowNum, destRowNum;

   theImage = [ NSBitmapImageRep imageRepWithContentsOfFile:filename ];
   if( theImage != nil )
   {
      bitsPPixel = [ theImage bitsPerPixel ];
      bytesPRow = [ theImage bytesPerRow ];
      if( bitsPPixel == 24 )        // No alpha channel
         texFormat[ texIndex ] = GL_RGB;
      else if( bitsPPixel == 32 )   // There is an alpha channel
         texFormat[ texIndex ] = GL_RGBA;
      texSize[ texIndex ].width = [ theImage pixelsWide ];
      texSize[ texIndex ].height = [ theImage pixelsHigh ];
      texBytes[ texIndex ] = calloc( bytesPRow * texSize[ texIndex ].height,
                                     1 );
      if( texBytes[ texIndex ] != NULL )
      {
         success = TRUE;
         theImageData = [ theImage bitmapData ];
         destRowNum = 0;
         for( rowNum = texSize[ texIndex ].height - 1; rowNum >= 0;
              rowNum--, destRowNum++ )
         {
            // Copy the entire row in one shot
            memcpy( texBytes[ texIndex ] + ( destRowNum * bytesPRow ),
                    theImageData + ( rowNum * bytesPRow ),
                    bytesPRow );
         }
      }
   }

   return success;
}


This is the method after loadGLtextures.
I tried putting the loadbitmap before the loadGLtextures, I got no more warnings but the white shape still remained.
Any suggestions?

----

The reason for the warning is that the compiler can't see the prototype at the time it is compiling the callsite.  You could add the prototype to the interface (in the header) or create a category in the implementation file (usually better for private method visibility).  Changing the order of the methods removes the warning since the callsite is now after the definition.

What does the actual rendering code look like?  Does it correctly bind the texture and then make the glTexCoord2f calls preceding each vertex?

--JeffDisher

----

If you're not sure whether it's the texture not getting loaded or the stuff not rendering properly, have a look at OpenGL Profiler. It can show you all of the textures loaded in your application, so you can directly see whether the texture has been loaded without your potentially buggy rendering code getting in the way. Beware, though, of textures that are white-on-transparent, they often look like they're not loaded and then you spend an hour running around in circles chasing the wrong thing! (Can you tell this happened to me?)

----

I am using glTexCoords after every vertice, I added the prototype to the header and got the texture to load fine.  Oh by the way alot of this code was stolen from NeHe tutorials (the one on texture mapping).  I appreciate the help thanks.

----
There are lots of NeHe examples that build and work with Cocoa unmodified.  What is different between what you wrote and the NeHe tutorial ?
Here are several common mbeginner mistakes:
- Enabled lighting when it shouldn't be.
- Disabled lighting when it shouldn't be.
- All lights are pointed away from the view frustrum.
- Texture mapping enabled but no texture is bound.
- Texture mapping not enabled.
- Geometry is edge-on to the coordinate system.
- Alpha blending is enabled but no blending function is defined.
- Open GL returned lots of errors and the code never checks them.
- The wrong texture unit is selected

[As the original poster of a giant blob of code, I have deleted the code.  I realized that I effectively spammed the page because the discussion is/was much too specific to a particular NeHe introductory example for my big (overly) general solution to benefit anybody.  If you want the code, you can find it in the page history.]
----

Before I answer that question, I have one more problem, I am trying to modify some code I got from NeHe thats loads a 256 by 256 image to load a 600 by 800 image. I really would like some help as to how to change the vertices. This bit of code is in drawRect:(NSRect)rect.

    
glPopMatrix();
   glDisable( GL_TEXTURE_GEN_S );   // Disable texture coord generation
   glDisable( GL_TEXTURE_GEN_T );   // Disable texture coord generation

   // This will select the BG texture
   glBindTexture( GL_TEXTURE_2D, texture[ filter * 2 ] );

   glPushMatrix();
   glTranslatef( 0.0f, 0.0f, -24.0f );
   glBegin( GL_QUADS );
   glNormal3f( 0.0f, 0.0f, 1.0f );
   glTexCoord2f( 0.0f, 0.0f );
   glVertex3f( -13.3f, -10.0f,  10.0f );
   glTexCoord2f( 1.0f, 0.0f );
   glVertex3f(  13.3f, -10.0f,  10.0f );
   glTexCoord2f( 1.0f, 1.0f );
   glVertex3f(  13.3f,  10.0f,  10.0f );
   glTexCoord2f( 0.0f, 1.0f );
   glVertex3f( -13.3f,  10.0f,  10.0f );
   glEnd();

   glPopMatrix();



----
Adjust the glVertex3f xy coords so that they have a 4:3 aspect ratio.  Meanwhile, check that non-power-of-two size textures works...

---- 
I tried changing the vertices and with no luck, I tried loading an image that was not a perfect square and with no luck either.  I may have changed the vertices incorrectly, I'm going to sound like an idiot, but can you give me some numbers for the x and y numerals and should I have changed the z at all?  I'm sorry I'm only a thirteen year old, and a beginner at OpenGL.  Also what do I do to change it so non-perfect-squares load? I appreciate all the help.


----
Do you have a glEnable(GL_TEXTURE_2D) somewhere early in the execution?  If you are having trouble getting started, you could try just hacking around within the tutorial to see what effect the changes that you make have on the OpenGL state.

--JeffDisher

