---
layout: page
title: TryToUsePerformSelectorOnMainThread
---



I have a main thread which is my Cocoa Application.  I want the user interface to remain responsive and am detatching another thread (when the user clicks a button) that will perform many computations.  What I want is that when this secondary thread exits I want a notification.  I read that performSelectorOnMainThread withObject waitUntilDone: was a "light weight" way to achive this.

What I have noticed is that:
 
* 1) when I detach the new thread the function it tries to call based on the argument for the selector is a class method
* 2) when I use performSelectorOnMainThread withObject waitUntilDone:  the function it calls based on the selector is also a class (rather than an instance) method
 


My question is whether there is a way to use an instance method because I would like the graphics to be updated once the computations are complete (but I cannot use General/OpenGL in the detached thread).  I will note the code segment I think are really germane by using the codesegment text.

As is the compiler compains that instance variablesis "accessed in a class method".  Since I am trying to send messages to an N<nowiki/>General/SOpenGLView object and some cells of an N<nowiki/>General/SForm object I need the instance of myself not the class (I think).


The program draws fractals.  So I want the computing to be performed independently of the GUI.
**
<<common.h>> **

    
/*
 *  common.h
 *  iFract
 *
 *  Created by Wayne Contello on 2/16/06.
 *  Copyright 2006 Wayne L. Contello. All rights reserved.
 *
 */
#import <time.h>

#import <Cocoa/Cocoa.h>


#define	SIZE_X		(600)
#define	SIZE_Y		(600)

typedef struct {
UInt32	x;
UInt32	y;
} coord ;

typedef struct {
	float	real;
	float	imag;
} imaginary;



typedef struct {
	imaginary		initialPhysicalLocation;
	float			step;
	unsigned int	escape;
	unsigned int	numColors;
//	unsigned int	**colorIndex;
	coord			pixels;
	clock_t			timeStart;
	clock_t			timeStop;
	unsigned int	pixelMap[SIZE_X][SIZE_Y];
	
} fracInit, *pfracInit;



 

**
<<iFract.h>> **
    
/* iFract */

#import <Cocoa/Cocoa.h>

#define TAG_X		(0)
#define TAG_Y		(1)
#define TAG_STEP	(2)
#define TAG_ESCAPE	(3)
#define TAG_COLORS	(4)


@interface iFract : General/NSObject
{
    General/IBOutlet id General/TopRightLoc;
    General/IBOutlet id General/BottomLeftLoc;
    General/IBOutlet id Status;
    General/IBOutlet id General/DrawButton;
    General/IBOutlet id General/TimeToCompute;
    General/IBOutlet id drawPort;
	General/IBOutlet id location;
	General/IBOutlet id progress;
	
	General/NSMutableData	*initData;
	
}
- (General/IBAction)	drawFractal:(id)sender;

#define DRAW
#define THREADS

**
<code>

#ifdef THREADS
+ (void)		performFractalDrawing: (id)anObject;
#else
- (void)		performFractalDrawing: (id)anObject;
#endif
</code>
**


- (void)		updateLocation;
- (void)		draw;
+ (void)		postDrawCleanup: (id)anObject;

@end
 

** <<iFract.m>> **
    

#import "iFract.h"
#import <General/OpenGL/gl.h>
#import <vecLib/vectorOps.h>

#import "common.h"


float complexMagnitudeSquared(imaginary *val);
unsigned int iterate(imaginary *initVal, imaginary *constant, float escapeMagSquared, unsigned int escapeIterations);


@implementation iFract

/*####################################*/
-(void)dealloc
/*####################################*/
{
[super dealloc];
}


/*####################################*/
- (void)awakeFromNib
/*####################################*/
{
imaginary initialLocation;
float step;

initialLocation.real = -0.74;
initialLocation.imag = 0;
step = 0.005;

[Status setStringValue: @"Ready"]; 
General/[TimeToCompute setStringValue: @"--"];
General/location cellAtIndex: [location indexOfCellWithTag: TAG_X setFloatValue: initialLocation.real];
General/location cellAtIndex: [location indexOfCellWithTag: TAG_Y setFloatValue: initialLocation.imag];
General/location cellAtIndex: [location indexOfCellWithTag: TAG_STEP setFloatValue: step];
General/location cellAtIndex: [location indexOfCellWithTag: TAG_ESCAPE setFloatValue: 8.0];
General/location cellAtIndex: [location indexOfCellWithTag: TAG_COLORS setIntValue: 64];

[self updateLocation];
}


**
<code>
/******************************************/
+(void)postDrawCleanup: (id)anObject;
/******************************************/
{
#if 1
pfracInit		init;

init = (fracInit *)[initData mutableBytes];


General/[DrawButton setEnabled: TRUE];
[Status setStringValue: @"Done"];

General/[TimeToCompute setFloatValue: ((float)init->timeStop - init->timeStart )/CLOCKS_PER_SEC ];
[self draw];
[initData release];
#endif

}
</code>
**

/******************************************/
-(void)draw
/******************************************/
{
//imaginary		initialVal, screenPos, screenStep, physicalPos, physicalStep, initialPhysicalPos;
//unsigned int	iterations, escape, numColors;
imaginary		screenPos, screenStep;
unsigned int	iterations, numColors;
coord			index;
pfracInit		init;

init = (fracInit *)[initData mutableBytes];

index.x = index.y = 0;
screenPos.real = screenPos.imag = -1;
screenStep.real = screenStep.imag = init->step;
numColors = init->numColors;

#ifdef DRAW
glClearColor( 0, 0, 0, 0 ) ;
glPointSize(1.0);
glClear( GL_COLOR_BUFFER_BIT ) ;
#endif

	for(index.y = 0; index.y < SIZE_Y; index.y++ ) {
		screenPos.real = -1;		
		
		for(index.x = 0; index.x < SIZE_X; index.x++ ) {
			// draw the point at the present location
#ifdef DRAW
			glBegin( GL_POINTS ) ;
			{
				glColor3f( 0.0f, ((float)iterations /numColors), 0.0f ) ;
				glVertex2f(screenPos.real, screenPos.imag ) ;
			}
			glEnd() ;
#endif			
			screenPos.real += screenStep.real;
			}

		screenPos.imag += screenStep.imag;
	}
#ifdef DRAW
glFlush() ;
#endif

}

/******************************************/
- (void)updateLocation
/******************************************/
{
imaginary initialLocation;
float step;

initialLocation.real	= General/location cellAtIndex: [location indexOfCellWithTag: TAG_X floatValue];
initialLocation.imag	= General/location cellAtIndex: [location indexOfCellWithTag: TAG_Y floatValue];
step					= General/location cellAtIndex: [location indexOfCellWithTag: TAG_STEP floatValue];

initialLocation.real += step*SIZE_X/2 ; // top right coordinates
initialLocation.imag += step*SIZE_Y/2 ; // top right coordinates

General/[TopRightLoc setStringValue: General/[NSString stringWithFormat: @"(%f, %f)", initialLocation.real, initialLocation.imag] ];

initialLocation.real -= step*SIZE_X ; // top right coordinates
initialLocation.imag -= step*SIZE_Y ; // top right coordinates

General/[BottomLeftLoc setStringValue: General/[NSString stringWithFormat: @"(%f, %f)", initialLocation.real, initialLocation.imag] ];
}

/******************************************/
- (General/IBAction)drawFractal:(id)sender
/******************************************/
{
fracInit		initParams;

//General/[DrawButton setEnabled: FALSE];
[Status setStringValue: @"Processing..."]; 
General/[TimeToCompute setStringValue: @"--"];

// Set up structure
initParams.step = General/location cellAtIndex: [location indexOfCellWithTag: TAG_STEP floatValue];
initParams.initialPhysicalLocation.real = General/location cellAtIndex: [location indexOfCellWithTag: TAG_X floatValue] - initParams.step*SIZE_X/2;
initParams.initialPhysicalLocation.imag = General/location cellAtIndex: [location indexOfCellWithTag: TAG_Y floatValue] - initParams.step*SIZE_Y/2;
initParams.escape						= General/location cellAtIndex: [location indexOfCellWithTag: TAG_ESCAPE floatValue];
initParams.numColors					= General/location cellAtIndex: [location indexOfCellWithTag: TAG_COLORS intValue];
initParams.pixels.x = SIZE_X;
initParams.pixels.y = SIZE_Y;

initParams.timeStart = clock();
	
[self updateLocation];


initData = General/[NSMutableData dataWithData: General/[NSData dataWithBytes: &initParams length: sizeof(fracInit)]];
[initData retain];

**
<code>
#ifdef THREADS
// wait for imaging of fractial to complete
General/[NSThread detachNewThreadSelector: @selector(performFractalDrawing:) toTarget:[self class] withObject: initData];
#else
[self performFractalDrawing: initData];
[self postDrawCleanup];
#endif
</code>
**
}

**
/********************************************************'/
#ifdef THREADS
+ (void)		performFractalDrawing: (id)anObject
#else
- (void)		performFractalDrawing: (id)anObject
#endif
/********************************************************'/
**
{
//imaginary		initialVal, screenPos, screenStep, physicalPos, physicalStep, initialPhysicalPos;
//unsigned int	iterations, escape, numColors;
//coord			index;
imaginary		physicalPos, physicalStep, initialPhysicalPos;
unsigned int	escape, numColors;
coord			index;

pfracInit		init;
General/NSAutoreleasePool *pool = General/[[NSAutoreleasePool alloc] init];

General/NSLog(@"In the thread");
init = (fracInit *)[anObject mutableBytes];

// Get initial parameters from structure
physicalStep.real = physicalStep.imag = init->step;

initialPhysicalPos.real = physicalPos.real = init->initialPhysicalLocation.real;
initialPhysicalPos.imag = physicalPos.imag = init->initialPhysicalLocation.imag;
escape = init->escape;
numColors = init->numColors;

index.x = index.y = 0;

	for(index.y = 0; index.y < SIZE_Y; index.y++ ) {
		physicalPos.real = initialPhysicalPos.real;
		
		for(index.x = 0; index.x < SIZE_X; index.x++ ) {
			init->pixelMap[index.x][index.y] = iterate(&physicalPos, &physicalPos, escape, numColors);
			physicalPos.real += physicalStep.real;
			}

		physicalPos.imag += physicalStep.imag;		
	}

init->timeStop = clock();
**
<code>
[self performSelectorOnMainThread: @selector(postDrawCleanup:) withObject: nil waitUntilDone: FALSE];
</code>
**
General/NSLog(@"Exiting the thread");

[pool release];
}

@end


/**********************************************'/
float complexMagnitudeSquared(imaginary *val)
/**********************************************'/
{
return(val->real*val->real + val->imag*val->imag);
}



/**********************************************'/
unsigned int iterate(imaginary *initVal, imaginary *constant, float escapeMagSquared, unsigned int escapeIterations)
/**********************************************'/
{
unsigned int	iterations;
imaginary		val;
float			tempReal;

iterations = escapeIterations;

val.real = initVal->real;
val.imag = initVal->imag;

while(escapeIterations) {
	tempReal = val.real;
	val.real = val.real*val.real - val.imag*val.imag + constant->real;
	val.imag = 2*tempReal*val.imag + constant->imag ; 
	
	if(escapeMagSquared < complexMagnitudeSquared(&val) ) {
		escapeIterations = iterations - escapeIterations;
		break;
		}
		
	escapeIterations--;
	}

return(escapeIterations);
}

 



----

The reason why the detached thread is calling a class method is because you told it to:
    
General/[NSThread detachNewThreadSelector: @selector(performFractalDrawing:) toTarget:[self class] withObject: initData];

(you are targeting *[self class]* instead of *self* so you are targeting the class, not the instance)

Similarly with the *performSelectorOnMainThread:* call.  You are sending it to self from within a class method so you are calling it on the class.

Does that help?

--General/JeffDisher

----

That does help.  While the answer was simple and I do understand the distinction (now) I did not really understand how sending "[self class]" was causing the problem.  I think the root of my problem was not understanding this.

--General/WayneContello

----

Okay, I will try to explain this.

Objective-C uses the OO concepts of General/SmallTalk (one of the purer OO languages - it is pretty sweet).  In General/SmallTalk, everything is an object (even blocks of code are objects).  Most importantly, is that classes are objects (they are instances of Metaclass, as I recall).

Classes have methods, just as instances have methods.  *self* refers to the object which received the message you are currently in (unless you assign self, but that is a corner case which doesn't usually apply outside of initializers).  *[self class]* is the object which is the class which *self* is an instance of.  Thus, messages sent to *self* will be received by the *self* instance.  Methods sent to *[self class]* will be received by the class itself (hence why it wanted to see a class method).  As to why the second method had to be a class method, *self* is going to refer to a class when inside a class method.

Does that explain it or is something still missing?

--General/JeffDisher
