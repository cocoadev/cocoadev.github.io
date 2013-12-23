---
layout: page
title: QuartzComposerWithinScreenSaverView
---



Hi There... 

I'm trying to create a ScreenSaver with the screensaver template in XCode. This is my first attempt at an app, so any help would be greatly appreciated.

I keep getting the error "invalid drawable" on the console when I run the saver. Any ideas?

Here's the code I've got so far:

    
#import "ScreenTest2View.h"
#define kRendererEventMask (NSLeftMouseDownMask | NSLeftMouseDraggedMask | NSLeftMouseUpMask | NSRightMouseDownMask | NSRightMouseDraggedMask | NSRightMouseUpMask | NSOtherMouseDownMask | NSOtherMouseUpMask | NSOtherMouseDraggedMask | NSKeyDownMask | NSKeyUpMask | NSFlagsChangedMask | NSScrollWheelMask | NSTabletPointMask | NSTabletProximityMask)
#define kRendererFPS 100.0

@implementation ScreenTest2View

- (id)initWithFrame:(NSRect)frame isPreview:(BOOL)isPreview
{
    self = [super initWithFrame:frame isPreview:isPreview];
    if (self) {
       
		 [self setAnimationTimeInterval:1/30.0];
		
		
	NSOpenGLPixelFormatAttribute	attributes[] = {
													
										NSOpenGLPFADoubleBuffer,
										NSOpenGLPFAAccelerated,
										NSOpenGLPFADepthSize, 24,
										(NSOpenGLPixelFormatAttribute) 0
										};
												
	NSOpenGLPixelFormat*			format = [[[NSOpenGLPixelFormat alloc] initWithAttributes:attributes] autorelease];
	_openGLContext = [[NSOpenGLContext alloc] initWithFormat:format shareContext:nil];
	
	if(_openGLContext == nil) {
		NSLog(@"Cannot create OpenGL context");
		[NSApp terminate:nil];
	}
	
	self openGLContext] makeCurrentContext];
    [_openGLContext setView: self];
	
	_filePath = [[[[NSBundle mainBundle] pathForResource:@"particles" ofType:@"qtz"];
	_renderer = [[QCRenderer alloc] initWithOpenGLContext:_openGLContext pixelFormat:format file:_filePath];
	if(_renderer == nil) {
		NSLog(@"Cannot create QCRenderer");
		[NSApp terminate:nil];
	}
		
	//Create a timer which will regularly call our rendering method
	_renderTimer = [[NSTimer scheduledTimerWithTimeInterval:(1.0 / (NSTimeInterval)kRendererFPS) target:self selector:@selector(_render:) userInfo:nil repeats:YES] retain];
	if(_renderTimer == nil) {
		NSLog(@"Cannot create NSTimer");
		[NSApp terminate:nil];
	}
		
	}
	
	
	
    return self;
}


- (void) renderWithEvent:(NSEvent*)event
{
	NSTimeInterval			time = [NSDate timeIntervalSinceReferenceDate];
	NSPoint					mouseLocation;
	NSMutableDictionary*	arguments;
	
	//Let's compute our local time
	if(_startTime == 0) {
		_startTime = time;
		time = 0;
	}
	else
	time -= _startTime;
	
	
	_screenSize.width = CGDisplayPixelsWide(kCGDirectMainDisplay);
	_screenSize.height = CGDisplayPixelsHigh(kCGDirectMainDisplay);
	
	//We setup the arguments to pass to the composition (normalized mouse coordinates and an optional event)
	mouseLocation = [NSEvent mouseLocation];
	mouseLocation.x /= _screenSize.width;
	mouseLocation.y /= _screenSize.height;
	arguments = [NSMutableDictionary dictionaryWithObject:[NSValue valueWithPoint:mouseLocation] forKey:QCRendererMouseLocationKey];
	if(event)
	[arguments setObject:event forKey:QCRendererEventKey];
	
	//Render a frame
	if(![_renderer renderAtTime:time arguments:arguments])
	NSLog(@"Rendering failed at time %.3fs", time);
	
	//Flush the OpenGL context to display the frame on screen
	[_openGLContext flushBuffer];
}

- (void) _render:(NSTimer*)timer
{
	//Simply call our rendering method, passing no event to the composition
	[self renderWithEvent:nil];
}

- (void) sendEvent:(NSEvent*)event
{
	[super sendEvent:event];
	
	//If the renderer is active and we have a meaningful event, render immediately passing that event to the composition
	if(_renderer && (NSEventMaskFromType([event type]) & kRendererEventMask))
	[self renderWithEvent:event];
	
	
}


Again, any help would be greatly appreciated. I know I'm missing something simple here, but I can't seem to figure it out.

Thanks,
Alexandre

----
Why not just add a QCView as a subview? ScreenSaverView<nowiki/>s are NSView<nowiki/>s and as such you have the full set of view machinery available to you.

----

Ok, I did what you suggested... however it can't seem to load my composition. It's in the resources folder and everything, so I don't understand why it's having a problem. Here's the code... again, help is greatly appreciated.

    

#import "ScreenTest4View.h"


@implementation ScreenTest4View

- (id)initWithFrame:(NSRect)frame isPreview:(BOOL)isPreview
{
    self = [super initWithFrame:frame isPreview:isPreview];
    if (self) {
		
		qcView = [[QCView alloc] init];
      
		if (!qcView) {
			NSLog(@"could not make qc view");
			}
			
		  [qcView setAutostartsRendering:YES]; 
		
		// am i missing something here??
		if ([qcView loadCompositionFromFile:[[NSBundle mainBundle] pathForResource:@"particles" ofType:@"qtz"]] == NO) {
			
			
				NSLog(@"could not load");
				}
		
		// set values for all input keys
		
		
		// ...
        [qcView setFrame:[self bounds]];
        [self addSubview:qcView];
		
        [self setAnimationTimeInterval:1/30.0];
		
		if ([qcView isRendering] == YES) {
		NSLog(@"no rendering");
		}
		
    }
    return self;
}

- (void)startAnimation
{
    [super startAnimation];
}

- (void)stopAnimation
{
    [super stopAnimation];
}

- (void)drawRect:(NSRect)rect
{
    [super drawRect:rect];
}

- (void)animateOneFrame
{
	
    return;
}

- (BOOL)hasConfigureSheet
{
    return NO;
}

- (NSWindow*)configureSheet
{
    return nil;
}

@end



----
When you're a screensaver, you're a plugin. As such,     [NSBundle mainBundle] is not you, it is whatever app loaded you.

A bit of quality time spent with the debugger and NSLog would answer your question.

----

Thanks so much. I figured it out..

