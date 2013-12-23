---
layout: page
title: AnimationTechniquesAndCocoa
---


**Animation Techniques and Cocoa**

*I'm leaving this up because the discussion is interesting; don't use my code, though!  It was written back when I was just learning how to program, so it's pretty lame.  -  JoeOsborn*

**Introduction**

	For a new programmer, the task of animation can be daunting; it was for me. How was I to know how to do things like NSAffineTransform, image compositing, drawing to the screen, or even implementing drawing at all?  Every piece of sample animation code I'd seen was cryptic, poorly-refactored, even hacky C and C++.  Though I'm comfortable with C, the concept of a 3D graphics library gives me the willies.  So I looked to Cocoa for an answer, being a new Cocoa programmer.  After toying with NSImage for a while, this is what I've discovered:

*Animation in Cocoa is more fun than a crate full of peanut-butter and jelly sandwiches(and it's a whole lot easier to implement)!*

	That notwithstanding, there were some road blocks; I changed my mind between using NSTimer and a more traditional(and less Cocoa) timing method, first concerned about NSTimer's unreliability when it's given NSTimeIntervals of less than .05, then disgusted by all the while() loops and the un-GOOD("GOOD Object-Oriented Design")ness of my code.  The only 

	The approach I will take will be purely Cocoa, with a helper class(OpenSwordGroup's OSAnimationTimer) of my own design which wraps the necessary NSTimer methods into a pleasant and easy-to-use class.  In this article, I'll show a bit of animation code and a bit of theory.  OSAnimationTimer can be found at http://opensword.dyndns.org/OSAnimationTimer.dmg.  (I won't bother going over the implementation of OSAnimationTimer; it's laughably simple.  OSAnimationTimer might be an example of a refactoring known as MethodObject; many of my classes implemented NSTimer convenience methods, so I bundled them all up into a single class.)




**Animation and Timing Techniques**

	Before any thought can be given to animation, a simple fact must be considered: movement is quantized.  Any motion can be divided into a number of parts, by time or by distance moved(or type of motion).  On a computer, it is easiest to quantize by time.  A "frame" of animation is simply a given picture of the animating object at a given location.  When different images are presented to a human in quick succession, the illusion of motion is created.  The speed at which frames are drawn is called the "framerate" and is measured in frames per second(fps).  **To best present the illusion of motion, framerate must be constant and fairly high**(television's framerate is 30 FPS; most games are either that or 60)** and the distance an object moves between frames must be fairly low**(in the following examples, the character will move 4 pixels every frame, for 8 frames; a total of 32 pixels will be moved.)**.**

	NSTimer is a powerful class, but its interface is unwieldy for one who wants to use it in a lot of different classes in different inheritance heirarchies.  Additionally, NSTimer usage generally requires creation of a method with an ugly extraneous argument.  OSAnimationTimer solves both of these problems.

	There isn't much to say for timing.  Usage of OSAnimationTimer is straightforward:

    
/*an instance variable, OSAnimationTimer*timer, has been initialized in -init
already.  another instance variable, frameNumber, is an unsigned integer which is 
accessed below to help make sure the entire animation occurs(NSTimer sometimes fires
unreliably).  A third instance variable, 'view', is the view into which this
object which animates will be drawn.  A fourth instance variable, drawPos, is
the NSPoint to which this object will be composited.  The image for this object
is 32 pixels by 32 pixels.*/

-(void)beginAnimation
{
	[self setFrameNumber:0];
	self timer] start];
}

-(double)framerate
{
	return 30;
}

-(void)prepareForNextFrame
{
	if([self frameNumber] < 9])  //we should animate
	//(really, I would put that into another messagesend, but that's just me).
	{
		[self incrementDrawPosXBy:4]; //a well-named method
		[[self view] displayRect:[[NSMakeRect([self drawPos].x, [self drawPos].y, 32, 32)]; 
		[self incrementFrameNumber];
	}
	else // done animating; let's clean up after ourselves.
	{
		self timer] stop];
		[[NSLog(@"Done moving.");
	}
}



	The above code will draw 30 frames every second.  Not very hard.  It can also be easily adapted to, say, switching frames while moving simply by checking which frameNumber it is.

	I believe that with this document and the example project, no animation task can be too scary.  Now, let's get started on some PB&J sandwiches.

**Common Problems with Animation**

Problem: Animation is jerky/too slow/too fast/ugly.

Solution: Try changing your -framerate method to return a different value; if it's too high, the animation will be jerky and fast, if it's too low, then the reason for the slowness is obvious.  Also, use an application like Sampler and make sure the problem doesn't have to do with your drawing code.  If the code which draws doesn't finish before the timer fires again, bad things happen to the illusion of motion.  Also, if you have many objects animating at the same time, there will be a slowdown.

Problem: Image doesn't seem to move at all.

Solution: Make sure you're telling the object's view to redraw the proper area;  make doubly sure that you're telling the right view to redraw.  You can use NSLogs or GDB for these purposes.  If you are doing what you're supposed to, try other things- make sure your drawing or animation code is called.  Be creative.

Remember-- programming is fun.

JoeOsborn

----

Discussion on timing techniques refactored to AnimationTimingAndCocoaDiscussion.

----

It looks like Apple is making some efforts in this area.... See NSAnimation? and NSViewAnimation?, both added in 10.4 .... I don't know about the rest of you, but I kind of wanted an easy way to do Quicksilver-like animations, and finding those articles in Apple's dev docs made me happy.

--

Does anyone have any working method for swapping the content view of a window with a fade (i.e. when switching prefence panes in sys prefs) This should be easy with NSViewAnimation, but my handful of tests produced horrible results - where should one begin?

There is some downloadable code in the CoreImage section which does what you want.

----

I just published Flipr, sample code to do widget-like window flipping - it's a category on NSWindow, and uses CoreImage and NSAnimation.

Details and downloads here: http://www.brockerhoff.net/src/index.html

*RainerBrockerhoff*

----

I am working on getting animations to work in an OpenGL-based game. The animation system is working, except that things are *very* jerky when the app first starts. My log for the first few frames is like this (FPS should be 60.0):
    
2009-03-15 15:04:48.885 Bouncies[3193] FPS: 3.834378
2009-03-15 15:04:48.974 Bouncies[3193] FPS: 12.164340
2009-03-15 15:04:48.992 Bouncies[3193] FPS: 79.368992
2009-03-15 15:04:49.007 Bouncies[3193] FPS: 120.530739
2009-03-15 15:04:49.024 Bouncies[3193] FPS: 96.836790
2009-03-15 15:04:49.040 Bouncies[3193] FPS: 108.278175
2009-03-15 15:04:49.057 Bouncies[3193] FPS: 93.553945
2009-03-15 15:04:49.074 Bouncies[3193] FPS: 93.796894
2009-03-15 15:04:49.090 Bouncies[3193] FPS: 92.304733
2009-03-15 15:04:49.107 Bouncies[3193] FPS: 92.786125
2009-03-15 15:04:49.124 Bouncies[3193] FPS: 92.626809
2009-03-15 15:04:49.140 Bouncies[3193] FPS: 110.243693
2009-03-15 15:04:49.157 Bouncies[3193] FPS: 91.523486
2009-03-15 15:04:49.174 Bouncies[3193] FPS: 106.859207
2009-03-15 15:04:49.190 Bouncies[3193] FPS: 91.882625
2009-03-15 15:04:49.207 Bouncies[3193] FPS: 93.182683
2009-03-15 15:04:49.233 Bouncies[3193] FPS: 71.447134
2009-03-15 15:04:49.240 Bouncies[3193] FPS: 139.969932
2009-03-15 15:04:49.257 Bouncies[3193] FPS: 62.150267
2009-03-15 15:04:49.274 Bouncies[3193] FPS: 61.950151
2009-03-15 15:04:49.290 Bouncies[3193] FPS: 61.822884
[FPS stabilizes at around 62 FPS]


Is there any way to fix this? Here's an outline of what my code looks like:
    
/*  
Since Cocoa doesn't call the init methods on views created in Interface
Builder, I used awakeFromNib to do my OpenGL initializations.
*/
- (void) awakeFromNib
{  
  {
    // set up OpenGL context, load sprites
  }

  // set up main timer
  NSMethodSignature *aSignature = 
    [MyOpenGLView instanceMethodSignatureForSelector: 
      @selector( calculateNextFrame )];
  timerInvocation = [NSInvocation invocationWithMethodSignature: aSignature];
  [timerInvocation setSelector: @selector( calculateNextFrame )];
  [timerInvocation setTarget: self];

  // sets up timer for 60.0 FPS
  theTimer = [NSTimer
          scheduledTimerWithTimeInterval: 1.0 / 60.0 
                              invocation: timerInvocation
                                 repeats:  YES];
  [theTimer retain];
    
  lastUpdate = [[NSDate date] retain];

}

- (void) drawRect: (NSRect) rect
{      
  { 
    // prepare OpenGL render system for drawing next frame
  }
  
  [self drawBackground: rect];
  [self drawForeground: rect]; // draws the ballSprite to the OpenGL view
  self openGLContext] flushBuffer]; 
}

/* 
calculateNextFrame is called by the game Timer. The method sets the new
drawing coordinates for each sprite, and then notifies the application that
the View needs to be updated with [self setNeedsDisplay:YES].

Sprite velocities are stored in px/sec, so each sprite is told how long it has
been since the last update.
*/
- (void) calculateNextFrame
{
  [ballSprite calculateNextWithRect:[self bounds]
                          timeDelta: (- [lastUpdate timeIntervalSinceNow]) ];

  [lastUpdate release];
  lastUpdate = [[[[NSDate date] retain];
  [self setNeedsDisplay: YES];
}


***UPDATE***
I fixed the problem.  The problem was that Cocoa does some more setup after it calls awakeFromNib:. That means that my drawing code was competing with these other setup procedures for processing resources, and that made the animation-drawing very slow and jerky.  By moving the timer-making code (which was in awakeFromNib:) to the method applicationDidFinishLaunching:, I made the animations wait until after application setup procedures were completed.

The result: my animation starts as smooth as butter.

