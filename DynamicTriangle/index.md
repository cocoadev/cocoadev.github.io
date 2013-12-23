---
layout: page
title: DynamicTriangle
---

I am posting this program I wrote sometime back, which makes a triangle using OpenGl. There are three text boxes, where the user can enter the initial velocities for each of the three triangle nodes. Then I specify the number of time steps this triangle should redraw itself doing what I ask it to do in the program (which normally is to change its angle randomly and move eaach node). I am using a button to start this process after I specify the velocities and the timesteps. This button is a "toggle" type NSButton which has its alternate title set to Pause. however once I start the program and once the program start redrawing the triangle, I cannot stop it in between. I am looking for ways in which I might be able to discontinue this operation/program by pressing the button again when it displays the "pause" state

    #import "Triangle.h"
#include <stdlib.h>
#include <time.h>
#include <OpenGL/gl.h>


@implementation Triangle

- (id) initWithCoder: (NSCoder *) coder
{
	 NSOpenGLPixelFormatAttribute attrs[] = 
    {
        NSOpenGLPFADoubleBuffer, nil
    };
	
    NSOpenGLPixelFormat* pixFmt ;
    long swapInterval ;
    
	self = [super initWithCoder: coder] ;
	ver1.x = 0.556;
	ver1.y = 0.2;
	ver2.x = -0.343;
	ver2.y = 0.443;
	ver3.x = 0.02;
	ver3.y = 0.66;
	vel1 = 0.;
	vel2 = 0.;
	vel3 = 0.;
	steps = 0;
	flipped = NO;
	a1 = time(NULL);	
	r = 0.0;
	g = 1.0;
	b = 0.0;
	angle1 = 2*3.14*[self generateNumber];
	angle2 = 2*3.14*[self generateNumber];
	angle3 = 2*3.14*[self generateNumber];


	
	pixFmt = [[NSOpenGLPixelFormat alloc] initWithAttributes: attrs] ;
    [self setPixelFormat: pixFmt] ;
    swapInterval = 1 ;
    self openGLContext] setValues: &swapInterval forParameter: [[NSOpenGLCPSwapInterval ] ;
	return self ;
}

-(void) awakeFromNib
{
[velocity1 setFloatValue:vel1];
[velocity2 setFloatValue:vel2];
[velocity3 setFloatValue:vel3];
[timesteps setFloatValue:steps];
}

-(float)generateNumber
	{
	srand(a1);
	int a2,b2;
	float c; 
	a2 = rand();
	a1 = a2;
	b2 = RAND_MAX;
	float tempa, tempb;
	tempa = a2;
	tempb = b2;
	c = tempa/tempb;
    return c;	
	}


-(NSPoint)boundaryCondition:(NSPoint)var
{
	NSPoint tempPoint;
	tempPoint.x = var.x;
	tempPoint.y = var.y;
	
	if (tempPoint.x < -1.0) 
	{tempPoint.x = tempPoint.x +2.0;
	flipped = YES;}
	if (tempPoint.x > 1.0)
	{ tempPoint.x = tempPoint.x -2.0;
	flipped = YES;}
	
	if (tempPoint.y < -1.0) 
	{ tempPoint.y = 2.0+ tempPoint.y;
	flipped = YES;}
	if (tempPoint.y > 1.0)
	{ tempPoint.y = tempPoint.y - 2.0;
	flipped = YES;}

	return tempPoint;	
}

- (IBAction)move:(id)sender
{
vel1 = [velocity1 floatValue];
vel2 = [velocity2 floatValue];
vel3 = [velocity3 floatValue];
steps = [timesteps floatValue];
int t;

for (t=0; t<=steps; t++)
	{
	float angle, interval;
	flipped = NO;
	interval =0.005;

	NSLog(@"Time Step %d", t);
	
	angle1 += [self generateNumber]*0.01;
	ver1.x = ver1.x + interval*vel1*cos(angle1);
	ver1.y = ver1.y + interval*vel1*sin(angle1);
	ver1 = [self boundaryCondition:ver1];
	
	angle2 += [self generateNumber]*0.01;
	ver2.x = ver2.x + interval*vel2*cos(angle2);
	ver2.y = ver2.y + interval*vel2*sin(angle2);
	ver2 = [self boundaryCondition:ver2];

	angle3 += [self generateNumber]*0.01;
	ver3.x = ver3.x + interval*vel3*cos(angle3);
	ver3.y = ver3.y + interval*vel3*sin(angle3);
	ver3 = [self boundaryCondition:ver3];

	[self lockFocus];
	[self drawRect:[self frame]];
	[self unlockFocus];
	}
}

-(void)drawRect:(NSRect)rect
{
	if (flipped == YES) {
	r = [self generateNumber];
	b = [self generateNumber];
	g = [self generateNumber];
	}
	
	glClearColor( 0, 0, 0, 0 ) ;
    glClear( GL_COLOR_BUFFER_BIT ) ;
    glColor3f( r,g,b ) ;
    glBegin( GL_TRIANGLES) ;
    {
        glVertex3f( ver1.x, ver1.y, 0.0 ) ;
        glVertex3f( ver2.x, ver2.y, 0.0 ) ;
	    glVertex3f( ver3.x , ver3.y, 0.0 ) ;
    }
    glEnd() ;
    glFinish() ;
	[[self openGLContext] flushBuffer];
}
@end

