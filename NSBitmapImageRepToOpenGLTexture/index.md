---
layout: page
title: NSBitmapImageRepToOpenGLTexture
---

Hi,

I am using NSBitmapImageRep's - (id)initWithFocusedViewRect:(NSRect)rect to load "screenshot" of view to NSBitmapImageRep. The next thing what I want to do is to use it as OpenGL texture in OpenGL drawing. I can easily load .bmp files to NSBitmapImageRep and then to OpenGL texture, but for some reason initWithFocusedViewRect doesn't work. Could anyone help me?

I am using code from one of the ports of NeHe tutorials. Here it is:
    
  BOOL success = FALSE;
   int bitsPPixel, bytesPRow;
   unsigned char *theImageData;
   int rowNum, destRowNum;
   NSLog(@"ziurim");
   if( theImage != nil )
   {
   NSLog(@"ne nilas");
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
        // theImageData=[theImage bitmapData];
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


Thanks,
Aidas

----

What exactly about -initWithFocusedViewRect: doesn't work? Is it not even making it to the copying of pixel data or is it giving you garbled textures or what? That code's worked fine for me in all sorts of uses for NSBitmapImageRep in the past. I suspect your problem lies in either some quirk of -initWithFocusedViewRect: or your handling of it from the time you create the NSBitmapImageRep to its use in the code above. Could you provide that code as well?

- JustinAnderson

----

Actually when I try to put that texture onto my quadrat it simply doesn't work (my quadrat becomes white). I've checked NSBitmapImageRep created using initWithFocusedViewRect:, i've wrote it to file using [bitmapimagerep tiffrepresentation] and it worked just fine. As I mentioned before I've created another texture sucesefully from .bmp file using NSBitmapImageRep imageRepWithContentsOfFile:. It worked just fine. Here is my code:
    
- (BOOL) loadGLTextures:(NSBitmapImageRep *)textureOne secondTexture:(NSBitmapImageRep *)textureTwo
{
   BOOL status = FALSE;
   if( [ self loadBitmap:textureOne intoIndex:0 ] )
   {
      status = TRUE;

      glGenTextures( 2, &texture[ 0 ] );   // Create the texture

      // Typical texture generation using data from the bitmap
      glBindTexture( GL_TEXTURE_2D, texture[ 0 ] );

      glTexImage2D( GL_TEXTURE_2D, 0, 3, texSize[ 0 ].width,
                    texSize[ 0 ].height, 0, texFormat[ 0 ],
                    GL_UNSIGNED_BYTE, texBytes[ 0 ] );
      // Linear filtering
      glTexParameteri( GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR );
      glTexParameteri( GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR );



 if( [ self loadBitmap:textureTwo intoIndex:1 ] )
   {
      status = TRUE;
      glBindTexture( GL_TEXTURE_2D, texture[ 1 ] );

      glTexImage2D( GL_TEXTURE_2D, 0, 3, texSize[ 1 ].width,
                    texSize[ 1 ].height, 0, texFormat[ 1 ],
                    GL_UNSIGNED_BYTE, texBytes[ 1 ] );
      // Linear filtering
      glTexParameteri( GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR );
      glTexParameteri( GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR );
   }



      free( texBytes[ 0 ] );
      free( texBytes[ 1 ] );
   }
   
   
   
        
   
   

   return status;
}


Also here is some other code that is calling loadGLTextures
    
NSBitmapImageRep *rep2=[NSBitmapImageRep imageRepWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"NeHe" ofType:@"bmp"]];
//i've tested [self takeShot] and it really works
BOOL suc=[cubeView loadGLTextures:[self takeShot] secondTexture:rep2];
-(NSBitmapImageRep *)takeShot
{
NSBitmapImageRep *rep=[[NSBitmapImageRep alloc] initWithFocusedViewRect:[textField frame]];// i am sure this is ok 

return [rep autorelease];
}

