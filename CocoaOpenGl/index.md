---
layout: page
title: CocoaOpenGl
---

How about some basic OpenGL Examples for MacOSX. Please comment your code.

----

Neon Helium Productions has an excellent series of OpenGL tutorials at http://nehe.gamedev.net/. There is Cocoa source code supplied for most of the tutorials.

**Most might be overstating it; at least, most of the really interesting ones (volumetric shadows, cel shaders, et cetera) don't have a Cocoa version. Good way to get you up to speed and ready to experiment, though. I'm still working on getting picking working properly.**

There are 38 Cocoa OpenGL lessons and code based on NeHe's tutorials, including cel shading, along with a lot of other interesting Cocoa OpenGL stuff here: http://www.withay.com/macosx/opengl/

Katherine Tattersall created the NeHe tutorials in Cocoa.  Go to http://divide.zerobyzero.ca/ -- JimCath

*However, her tutorials end at number 9, which is hardly the extent of NeHe's tutorials, and those ported at withay.com*

----
FadeFromBlack is a simple OpenGL screen saver. The code might be commented.

CustomGLView is a simple demonstration of drawing with OpenGL in a custom NSView without subclassing NSOpenGLView.

----

**Cocoa Z-Buffer**

I used the CocoaInitGL example (availible from the apple sample code site) to init an opengl context into and NSOpenGLView...but then - when I start making...let's say, a cube - all the sides are rendered wrong...there ain't no Z-Buffer...so some sides shine through some others.....how to turn on the Z-buffer?

/Anton

You need to call glEnable(GL_DEPTH_TEST)
even though the example gives you a context that has depth buffers might not mean that opengl knows that it is supposed to do depth buffering.

Interestingly enough in the NSOpenGLView example this works if you put that call into the contextCreateContextButton method it does not work when the call is in initWithFrame, somehow the context that is created the first time does not contain a depth buffer.

To see for yourself, add a button to the sample in the button call glEnable and glDisable with GL_DEPTH_TEST, first context it does not work any other context created afterwards (with depth buffers enabled) this will work.

You only need to call glEnable once during your program (unless you turn it off again)

-- HaRald
----

If you use 
        glEnable(GL_DEPTH_TEST);
then there is a z-buffer, but you need to construct the cube in order (back to front). Refer to the Open GL Red/Blue book.

-- DavidRemahl

----

You don't need to draw the cube in any particular order if there is a z-buffer. That's one of the main benefits of a Z-buffer. In fact, it would be less efficient to construct the cube from back to front order on some architectures when you have a z-buffer enabled. A depth test is faster than a depth test then setting a pixel. Not drawing back to front is one of the main purposes of a z-buffer, since there are many times when you can't draw shapes if they require such an ordering.

You also need to remember when you call glClear to use the GL_DEPTH_BUFFER_BIT as well as the standard GL_COLOR_BUFFER_BIT, otherwise you can get some of the strangest drawing bugs you will ever see in your life. 

-- DaveFayram

----

I've tried to write some opengl code but no luck. I guess it is all about that thing called z-buffer. Could someone please tell me what i am doing wrong? I am using this code with CustomGLView. Here is my code:
    
- (void)drawRect:(NSRect)frameRect {
[oglContext setView:self];
long opaque = NO;
[[NSOpenGLContext currentContext] setValues:&opaque forParameter:NSOpenGLCPSurfaceOpacity];
[[NSColor redColor] set];
NSRectFill([self bounds]);
self window] setOpaque:NO];
[[self window] setAlphaValue:.999f];
        float aspectRatio;
//glClearDepth(-10.0);


   glViewport( 0, 0, [self frame].size.width, [self frame].size.height );
   glMatrixMode( GL_PROJECTION );   // Select the projection matrix
   glLoadIdentity();                // and reset it
   // Calculate the aspect ratio of the view
   gluPerspective( 45.0f, [self frame].size.width / [self frame].size.height,0.1f, 50.0f );
   glMatrixMode( GL_MODELVIEW );    // Select the modelview matrix
   glLoadIdentity();                // and reset it


        // inform the context that the view has been resized
        [oglContext update];
        glClear( GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT );
        glLoadIdentity(); 
        // adjust the viewport and frustum transforms
        // (gluLookAt() doesn't work properly)
        
        //glViewport(0, 0, frameRect.size.width, frameRect.size.height);
      //  glMatrixMode(GL_PROJECTION);s
        
        aspectRatio = (float) frameRect.size.width / (float) frameRect.size.height;
        //glFrustum(-aspectRatio, aspectRatio, -1.0, 1.0, 2.0, 100.0);

        // prepare for primitive drawing
        glMatrixMode(GL_PROJECTION);
        //glLoadIdentity();
        
        /*glEnableClientState( GL_VERTEX_ARRAY );

        // submit the vertex information for drawing
        glVertexPointer( 3, GL_FLOAT, 0, verts );

        glDrawElements( GL_TRIANGLES, 3, GL_UNSIGNED_BYTE, tris );
        glDisableClientState( GL_VERTEX_ARRAY );*/
        glEnable( GL_DEPTH_TEST );
        glTranslatef( 0.0f, 0.0, -5.4f );  
        if (move)
        {
        [[NSLog(@"ROATE");
        yrot += 0.5f;
        //yrot=50.3f;
        glRotatef( yrot, 0.0f, 1.0f, 0.0f );
        }
        glRotatef( 50.0, 1.0f, 0.0f, 0.0f );
         glBindTexture( GL_TEXTURE_2D, texture[ 0 ] );
   glBegin( GL_QUADS );
   
   
   
      // Front face
 /*  glTexCoord2f( 0.0f, 0.0f );
   glVertex3f( -1.0f, -1.0f,  1.0f );   // Bottom left
   glTexCoord2f( 1.0f, 0.0f );
   glVertex3f(  1.0f, -1.0f,  1.0f );   // Bottom right
   glTexCoord2f( 1.0f, 1.0f );
   glVertex3f(  1.0f,  1.0f,  1.0f );   // Top right
   glTexCoord2f( 0.0f, 1.0f );
   glVertex3f( -1.0f,  1.0f,  1.0f );   // Top left
   */
   
   // Front face
   glTexCoord2f( 0.0f, 0.0f );
   glVertex3f( -1.0f, -1.0f,  1.0f );   // Bottom left
   glTexCoord2f( 1.0f, 0.0f );
   glVertex3f(  1.0f, -1.0f,  1.0f );   // Bottom right
   glTexCoord2f( 1.0f, 1.0f );
   glVertex3f(  1.0f,  1.0f,  1.0f );   // Top right
   glTexCoord2f( 0.0f, 1.0f );
   glVertex3f( -1.0f,  1.0f,  1.0f );   // Top left

   // Back face
   glTexCoord2f( 1.0f, 0.0f );
   glVertex3f( -1.0f, -1.0f, -1.0f );   // Bottom right
   glTexCoord2f( 1.0f, 1.0f );
   glVertex3f( -1.0f,  1.0f, -1.0f );   // Top right
   glTexCoord2f( 0.0f, 1.0f );
   glVertex3f(  1.0f,  1.0f, -1.0f );   // Top left
   glTexCoord2f( 0.0f, 0.0f );
   glVertex3f(  1.0f, -1.0f, -1.0f );   // Bottom left

   // Top face
   glTexCoord2f( 0.0f, 1.0f );
   glVertex3f( -1.0f,  1.0f, -1.0f );   // Top left
   glTexCoord2f( 0.0f, 0.0f );
   glVertex3f( -1.0f,  1.0f,  1.0f );   // Bottom left
   glTexCoord2f( 1.0f, 0.0f );
   glVertex3f(  1.0f,  1.0f,  1.0f );   // Bottom right
   glTexCoord2f( 1.0f, 1.0f );
   glVertex3f(  1.0f,  1.0f, -1.0f );   // Top right

   // Bottom face
   glTexCoord2f( 1.0f, 1.0f );
   glVertex3f( -1.0f, -1.0f, -1.0f );   // Top right
   glTexCoord2f( 0.0f, 1.0f );
   glVertex3f(  1.0f, -1.0f, -1.0f );   // Top left
   glTexCoord2f( 0.0f, 0.0f );
   glVertex3f(  1.0f, -1.0f,  1.0f );   // Bottom left
   glTexCoord2f( 1.0f, 0.0f );
   glVertex3f( -1.0f, -1.0f,  1.0f );   // Bottom right

   // Right face
   glTexCoord2f( 1.0f, 0.0f );
   glVertex3f(  1.0f, -1.0f, -1.0f );   // Bottom right
   glTexCoord2f( 1.0f, 1.0f );
   glVertex3f(  1.0f,  1.0f, -1.0f );   // Top right
   glTexCoord2f( 0.0f, 1.0f );
   glVertex3f(  1.0f,  1.0f,  1.0f );   // Top left
   glTexCoord2f( 0.0f, 0.0f );
   glVertex3f(  1.0f, -1.0f,  1.0f );   // Bottom left

   // Left face
   glTexCoord2f( 0.0f, 0.0f );
   glVertex3f( -1.0f, -1.0f, -1.0f );   // Bottom left
   glTexCoord2f( 1.0f, 0.0f );
   glVertex3f( -1.0f, -1.0f,  1.0f );   // Bottom right
   glTexCoord2f( 1.0f, 1.0f );
   glVertex3f( -1.0f,  1.0f,  1.0f );   // Top right
   glTexCoord2f( 0.0f, 1.0f );
   glVertex3f( -1.0f,  1.0f, -1.0f );   // Top left
   glEnd();

        glFlush();
NSLog(@"pabaiga");
}


Thanks!

----

I'm no OpenGL expert, nor am I a cocoa expert, but my work is deeply tied to opengl for visualization, and as such I've written a basic GLView class which does what I need (tm).

My guess is that you've not set the correct pixel attributes, since as far as I can tell, that's where (in Cocoa) you turn on the depth buffer functionality (later you *actually* turn it on with a glEnable( GL_DEPTH_TEST ) and glDepthFunc( GL_LESS )

Here's my initWithFrame:

    

- (id) initWithFrame: (NSRect) frame
{
	renderTimer = nil;
	period = 0; averagePeriod = 0;	

	GLuint attribs[] = 
	{
		NSOpenGLPFANoRecovery,
		NSOpenGLPFAWindow,
		NSOpenGLPFAAccelerated,
		NSOpenGLPFADoubleBuffer, 
		NSOpenGLPFAColorSize, 24,
		NSOpenGLPFAAlphaSize, 8,
		NSOpenGLPFADepthSize, 24, //<-- z-buffer
		NSOpenGLPFAStencilSize, 8,
		NSOpenGLPFAAccumSize, 0,
		0
	};

	NSOpenGLPixelFormat* fmt = 
		[[NSOpenGLPixelFormat alloc] initWithAttributes: (NSOpenGLPixelFormatAttribute*) attribs]; 
		
	return self = [super initWithFrame:frame pixelFormat: [fmt autorelease]];
}

/*
    Call this in your awakeFromNib or somewhere else.
*/
- (void) initGL
{
	 self openGLContext ] makeCurrentContext ];
}



I could be wrong, but this has worked for me. 
--[[ShamylZakariya
----

Could someone give me a link to a simple OpenGL tutorial which draws simble cube with different textures on every side? Is it difficult?


----

NeHe is mentioned above. Go there. They have a texture-mapped cube example. Go there, it's not that hard. They even have cocoa example code for most of their tutorials. Go there.

http://nehe.gamedev.net/lesson.asp?index=02

--ShamylZakariya


----

I want to select a 3D object in Ortho mode, but it is not working. the input argument is already transform to the OpenGL coordinate.

    
-(void)select:(NSPoint)clickPoint
{
	GLuint  selectBuffer[64] = {0};
	GLint	hits, viewPort[4];
	
	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
	
	glSelectBuffer(64, selectBuffer);
	glGetIntegerv(GL_VIEWPORT, viewPort);
	
	glMatrixMode(GL_PROJECTION);
	glPushMatrix();
	
	glRenderMode(GL_SELECT);
	
	glLoadIdentity();
	gluPickMatrix(clickPoint.x, clickPoint.y, 20, 20, viewPort);

	glPopMatrix();
	glMatrixMode(GL_MODELVIEW);
	glLoadIdentity();
	GLfloat height	= viewPort[3];
	GLfloat width	= viewPort[2];
	GLfloat nRange	= 100.0f;
	glOrtho(-nRange, nRange, 
			-nRange*height/width,nRange*height/width,
			0,-nRange*4.0f);
	
	[self cleanScreen];
	[self showAll];  // call all glLists here and [context flushbuffer] at tail
	
	hits = glRenderMode(GL_RENDERER);

	glMatrixMode(GL_PROJECTION);
	glPopMatrix();
	
	glMatrixMode(GL_MODELVIEW);
	glLoadIdentity();
	
	NSLog(@"hits = %d", hits);
	NSLog(@"buffer = %d,%d,%d",selectBuffer[0],selectBuffer[1],selectBuffer[2]);
	[self showAll];
	}



After call this method I can not [self showAll] any more, it's similar as "can not quit the GL_SELECT mode".

