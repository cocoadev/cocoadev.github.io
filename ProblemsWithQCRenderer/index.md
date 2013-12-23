---
layout: page
title: ProblemsWithQCRenderer
---

I have some refreshing problems with QCRenderer.  I get small lines running down the screen and the it doesn't look as if the views refreshing correctly. Also I'm using 2 QCRenderers one streaming video to the other, because I'm constantly loading a new Quartz Composition and want the video to continue.  I use an NSTimer but maybe a CVDisplayLink would be better, as used in the apple Performer sample code.

Heres my code:

    
- (void)applicationDidFinishLaunching:(NSNotification *)notification {
               . . .
       NSOpenGLPixelFormatAttribute	attributes[] = {NSOpenGLPFAAccelerated, NSOpenGLPFANoRecovery, NSOpenGLPFABackingStore, NSOpenGLPFADoubleBuffer, NSOpenGLPFADepthSize, 24, 0};
	long swapInterval = 1;
	NSString *videoPath;
	NSString *path;
	
	[context setValues:&swapInterval forParameter:NSOpenGLCPSwapInterval];
	[context makeCurrentContext];
	pixelFormat = [[NSOpenGLPixelFormat alloc] initWithAttributes:attributes];
	context = [[NSOpenGLContext alloc] initWithFormat:pixelFormat shareContext:nil];
	
	if(context == nil)
	{
		NSWindow *infoWindow;

		infoWindow = NSGetCriticalAlertPanel( @"OpenGL",
                                         @"Faile to create NSOpenGLContext.",
                                         @"OK", nil, nil );
		[ NSApp runModalForWindow:infoWindow ];
		[ infoWindow close ];
		[ NSApp terminate:self ];
	}
	[context setView:qcView];
	videoPath = [[NSBundle mainBundle] pathForResource:@"Video" ofType:@"qtz"];
	path = [[NSBundle mainBundle] pathForResource:@"None" ofType:@"qtz"];
	
	videoRenderer = [[QCRenderer alloc] initWithOpenGLContext:context pixelFormat:pixelFormat file:videoPath];
	renderer = [[QCRenderer alloc] initWithOpenGLContext:context pixelFormat:pixelFormat file:path];

	
	#define kRendererFPS 60.0
 
	renderTimer = [[NSTimer scheduledTimerWithTimeInterval:(1.0 /
                                (NSTimeInterval)kRendererFPS)
                        target:self
                        selector:@selector(renderFrame:)
                        userInfo:nil
                        repeats:YES]
						retain];
	
	[[NSRunLoop currentRunLoop] addTimer:renderTimer
                                 forMode:NSEventTrackingRunLoopMode];
}
- (void)renderFrame:(NSTimer *)timer
{
	[self renderWithEvent:nil];
}

- (void) renderWithEvent:(NSEvent*)event
{
    NSTimeInterval  time = [NSDate timeIntervalSinceReferenceDate];
	
	if(startTime == 0)
    {
        startTime = time;
        time = 0;
    }
    else
        time -= startTime;
	
	
	[videoRenderer renderAtTime:time arguments:nil];
	[renderer setValue:[videoRenderer valueForOutputKey: @"Video"] forInputKey:@"Image"];
	[renderer renderAtTime:time arguments:nil];
	[context flushBuffer];
	
}


----

You're not running foul of this are you?: http://developer.apple.com/technotes/tn2005/tn2133.html  In particular it recommends: "Avoid, if possible, the functions that request an immediate flush to the screen. Applications drawing with Quartz should use CGContextSynchronize rather then CGContextFlush.

In Cocoa use NSView's setNeedsDisplay: to request an update for a view instead of the more immediate display:, and in Carbon's HIToolBox use HIViewSetNeedsDisplay or HIViewSetNeedsDisplayInRegion instead of the immediate HIWindowFlush."

Also, did you really mean to call methods on your context before it's even allocated?

I'm no OpenGL expert, but this code doesn't look right to me. What's happening in qcView? Its      - drawRect: method should be doing something to get the OpenGL content onto the screen, for example by calling the code that you are calling from your timer callback. The timer callback should just be flagging an update on the view using     [qcView setNeedsDisplay:YES];. This is the same update pathway that any Cocoa graphics would use - as far as I can see OpenGL is no different in this regard. The code at http://developer.apple.com/documentation/GraphicsImaging/Conceptual/OpenGL-MacProgGuide/opengl_drawing/chapter_3_section_3.html#//apple_ref/doc/uid/TP40001987-CH404-DontLinkElementID_15 may be enlightening.  --GC

---- 
Shortly after posting the code I realized the mistake of calling the methods for the context befored allocating memory.
If you look at apple's instructions for rendering a Quartz Composition in an NSView, I setup my rendering routine almost completely identical to theirs.
http://developer.apple.com/documentation/GraphicsImaging/Conceptual/QuartzComposer/qc_play_renderer/chapter_6_section_1.html#//apple_ref/doc/uid/TP40001357-CH209-TPXREF101

----
I have another problem a have forgotten to mention.  When using expose and dragging the window my video starts to flash on and off while dragging.  Does anybody know whats going on?

----

One very big difference between Apple's sample code and yours (apparently, as I'm only inferring) is that Apple's code is in full screen mode, and yours is rendering to a view. This might not appear to matter but it does, because in fullscreen mode OpenGL can flush directly to the video RAM and not to the window's back buffer. For use in a view, you have to handle the update differently, and conventionally. In other words, what is going on on qcView? PostYourCode. --GC

I think what you should be doing is something like:

    

// modify code above to:
- (void) renderFrame:(NSTimer *)timer
{
	[qcView setNeedsDisplay:YES];
}


// and in qcView:
- (void) drawRect:(NSRect) rect
{
    [theObjectAbove renderWithEvent:nil];
}




----  
From http://developer.apple.com/documentation/GraphicsImaging/Reference/QuartzFramework/Classes/QCRenderer_Class/Reference/Reference.html#//apple_ref/occ/instm/QCRenderer/renderAtTime:arguments:

"The     rendertAtTime:arguments: method does not swap the front and back buffers of the OpenGL context. You must perform the swap yourself by calling the OpenGL command     flushBuffer on the context associated with the renderer. This allows you to combine Quartz Composer with custom OpenGL code."

Now please will someone try to help me with the problem of the flashing video when using espose', and dragging the window?

----

If you are aiming that comment at me: "...stop being rude and ignorant", then I will cease to try and help. As far as I can see I didn't erase any part of what has been added to this page, though someone else might have - though no-one else seems to have stepped in here. The fact is, you're the one being ignorant. I've explained to you what the problem is - you are not handling the update of your view correctly. Even knowing relatively little about OpenGL (but a lot about Cocoa generally) a very superficial reading of the appropriate docs revealed what's needed. If you can't figure out how to read and interpret docs correctly, and then have a snipe at the only person who has bothered to try and help you, then frankly mate, you just ain't going to get far. It's obvious that you are not THINKING, as the silly bugs in your code showed. This is not a forum for dumping your problems on others and having them magically solved - given a patient and pleasant attitude you might be able to get some hints as to what the problem might be, and that's all. The comments above regarding     - renderAtTime:arguments: and      -flushBuffer are not where the problem lies, which should be obvious if you'd take the trouble to understand what I'm trying to tell you. Apple's code renders full-screen, and yours (apparently, as you haven't posted enough of it for me to be sure) does not. There is a very big difference in the destination for the OpenGL surface when you do one or the other, requiring the code to be written slightly differently to allow for it. I even wrote the code I think you need, and you're still bloody ungrateful. It may be my code isn't correct - fair enough, with most people that would at least eliminate that avenue and lead to investigation in another. But ranting and raving doesn't win friends - do you suppose anyone here is obliged to help you? If you bothered to investigate this, all your problems might well be fixed, but since instead you've decided to piss me off, this will be my last contribution to this problem, and I will not make the mistake of trying to help you out in the future. --GC

----
I offer you my apologies sir.  I realize that I myself have been ignorant and rude to you when all you were trying to do was help me.  I have taken your advice and am reading more into NSOpenGLContext and drawing to custom views.  Although I resent the part about the silly bugs. I said I caught the allocating mistake, didn't I?

