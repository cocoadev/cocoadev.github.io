---
layout: page
title: VideoStopsWhenOpeningDrawer
---

Is there any way to continue rendering a General/QCRenderer while clicking buttons or resizing your window?  I have a custom view rendering a Quartz composition with a General/QCRenderer and a video feed in the composition and the video feed refreshs with an General/NSTimer, and when I click a button or anything like that it freezes the General/NSView that I'm rendering to.  Also if I do expose and then move the window I get a repeated flashing in the General/NSView.  Is there anyway to fix this?

Heres the code:
    
      . . .
//I looked to the Quartz Composer Programming guide for some of this
General/NSOpenGLPixelFormatAttribute	attributes[] = {General/NSOpenGLPFAAccelerated, General/NSOpenGLPFANoRecovery,       General/NSOpenGLPFADoubleBuffer,    General/NSOpenGLPFADepthSize, 24, 0};
	long swapInterval = 1;
	General/NSString *path;
	General/NSString *videoPath;
	
	pixelFormat = General/[[NSOpenGLPixelFormat alloc] initWithAttributes:attributes];
	context = General/[[NSOpenGLContext alloc] initWithFormat:pixelFormat shareContext:nil];
	[context setValues:&swapInterval forParameter:General/NSOpenGLCPSwapInterval];

	//We need to know when the rendering view frame changes so that we can update the General/OpenGL contex
	
	[context setView: qcView];
	path = General/[[NSBundle mainBundle] pathForResource:@"Billboard" ofType:@"qtz"];
	videoPath = General/[[NSBundle mainBundle] pathForResource:@"Video" ofType:@"qtz"];
	
	videoRenderer = General/[[QCRenderer alloc] initWithOpenGLContext:context pixelFormat:pixelFormat file:videoPath];
	renderer = General/[[QCRenderer alloc] initWithOpenGLContext:context pixelFormat:pixelFormat file:path];

	//Render timer for rendering Quartz and feeding it the video.
	#define kRendererFPS 30.0
 
	renderTimer = General/[[NSTimer scheduledTimerWithTimeInterval:(1.0 /
                                (General/NSTimeInterval)kRendererFPS)
                        target:self
                        selector:@selector(renderFrame:)
                        userInfo:nil
                        repeats:YES]
                        retain];
}
- (void)renderFrame:(General/NSTimer *)timer
{
	[self renderWithEvent:nil];
}
- (void) renderWithEvent:(General/NSEvent*)event
{
    General/NSTimeInterval  time = General/[NSDate timeIntervalSinceReferenceDate];
	
	if(startTime == 0)
    {
        startTime = time;
        time = 0;
    }
    else
        time -= startTime;
	[videoRenderer renderAtTime:time arguments:nil];
	[renderer setValue:[videoRenderer valueForOutputKey:@"Video"] forInputKey:@"Image"];
	[renderer renderAtTime:time arguments:nil];
	[context flushBuffer];
}


----

You need to add your timer to additional run-loop modes. The sheduledTimer... call only adds it to the default runloop mode which is interrupted by resizes, menu pulldowns and lots of other stuff. From the docs: "The General/NSTimer class method scheduledTimerWithTimeInterval:invocation:repeats:, for example, creates a new timer object and adds it to the General/NSDefaultRunLoopMode mode of the current run loop. If you instead create the timer with timerWithTimeInterval:invocation:repeats:, you must add it manually to the run loop with the General/NSRunLoop instance method addTimer:forMode:, which allows you to specify a different mode." The mode you need to add it to is General/NSEventTrackingRunLoopMode. This is basically the same problem as General/NSTimerDoesntRunWhenMenuClicked --General/GrahamCox

----
Thanks, really appreciate it.
