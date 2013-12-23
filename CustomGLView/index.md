---
layout: page
title: CustomGLView
---



Here is an example of how to do Cocoa OpenGL drawing without using Apple's NSOpenGLView class. It requires that you drag an NSView custom view into the window in InterfaceBuilder and then set it to the customGLView class. This example also uses the customGLView as the application delegate in order to initialize the context's view to itself when the application is finished launching.

March 2nd, 2006: I tried using this code and found a few problems. First, there's a missing closing curly brace that prevents the compiler from recognizing the drawRect: method. After fixing that, I found that the window is blank (no triangles show up at all), and the window shows some garbage when you resize it.  Can anyone provide some suggestions on how to find the problems?

March 3rd, 2007: I've fixed the example below, based on <a href="http://developer.apple.com/samplecode/Custom_Cocoa_OpenGL/index.html">Apple's Custom Cocoa OpenGL sample code</a>. I've tested the code below on 10.4.


    
// customGLView.h

#import <AppKit/AppKit.h>
#import <OpenGL/OpenGL.h>
#import <OpenGL/gl.h>


@interface customGLView : NSView {

	NSOpenGLContext *oglContext;
	GLfloat *verts;
	GLubyte *tris;
}

@end
// ---------


// customGLView.m


#import "customGLView.h"


@implementation customGLView


/* default initializer for descendents of NSView */
- (id)initWithFrame:(NSRect)frame {

  self = [super initWithFrame:frame];
  if(self == nil)
    return nil;
  
  // create and activate the context object which maintains the OpenGL state
  oglContext = [[NSOpenGLContext alloc] initWithFormat: [NSOpenGLView defaultPixelFormat] shareContext: nil];
  [oglContext makeCurrentContext];
  
  // set the background colour
  glClearColor (0.0, 0.0, 1.0, 1.0);
  glClearDepth(1.0);
  
  // define a polygon primitive. first allocate some space for the data
  verts = calloc( 3*3, sizeof(GLfloat) );
  tris = calloc( 3, sizeof(GLubyte) );
  
  // initialize the values for the triangle's vertices and indices
  // this triangle is perpendicular to the default view vector
  if( verts != NULL && tris != NULL ) {
    verts[0] = 1.0f;
    verts[1] = -1.0f;
    verts[2] = -5.0f;
    verts[3] = -1.0f;
    verts[4] = -1.0f;
    verts[5] = -5.0f;
    verts[6] = 0.0f;
    verts[7] = 0.5f;
    verts[8] = -5.0f;
    
    tris[0] = 0;
    tris[1] = 2;
    tris[2] = 1;
  }
  else {
    // if we couldn't get the memory
    [self release];
    self = nil;
  }
  
  return self;
}

- (void)lockFocus
{
  NSOpenGLContext* context = oglContext;
  
  // make sure we are ready to draw
  [super lockFocus];
  
  // when we are about to draw, make sure we are linked to the view
  // It is not possible to call setView: earlier (will yield 'invalid drawable')
  if ([context view] != self) {
    [context setView:self];
  }
  
  // make us the current OpenGL context
  [context makeCurrentContext];
}
  
// this is called whenever the view changes (is unhidden or resized)
- (void)drawRect:(NSRect)frameRect {
  
  float aspectRatio;
  
  // inform the context that the view has been resized
  [oglContext update];
  
  glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
  
  // adjust the viewport and frustum transforms
  // (gluLookAt() doesn't work properly)
  glViewport(0, 0, frameRect.size.width, frameRect.size.height);
  glMatrixMode(GL_PROJECTION);
  glLoadIdentity();
  aspectRatio = (float) frameRect.size.width / (float) frameRect.size.height;
  glFrustum(-aspectRatio, aspectRatio, -1.0, 1.0, 2.0, 100.0);
  
  // prepare for primitive drawing
  glMatrixMode(GL_MODELVIEW);
  glLoadIdentity();
  glColor3f( 1.0, 0.5, 1.0 );
  glEnableClientState( GL_VERTEX_ARRAY );
  
  // submit the vertex information for drawing
  glVertexPointer( 3, GL_FLOAT, 0, verts );
  
  glDrawElements( GL_TRIANGLES, 3, GL_UNSIGNED_BYTE, tris );
  glDisableClientState( GL_VERTEX_ARRAY );
  
  glFlush();
}

// this tells the window manager that nothing behind our view is visible
-(BOOL) isOpaque {
  
  return YES;
}

-(void) dealloc {
  
  // clean ourselves up nicely by freeing our dynamic memory allocations
  if( verts )
    free( verts );
  if( tris )
    free( tris );
  
  [super dealloc];
}

@end



