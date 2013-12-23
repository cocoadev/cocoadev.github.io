---
layout: page
title: SillyOpenGLQuestion
---



My simulation framework was running much faster than realtime (3.5x, on average), and so  -- alongside writing a frame-rate capper -- I decided to add full antialiasing.

The antialiasing works great, but I can't turn it off! I set it up to be a configurable option, but it has no effect.

First off, here's my initialization:

    
- (id) initWithFrame: (NSRect) frame
{
	movieImageBuf = NULL; //c-ptr
	
	renderTimer = nil;
	movieOutputFolder = nil;
	movieController = nil;
	
	period = averagePeriod = currentFrame = movieFrame = 0;
	recording = antialias = NO;	

	GLuint attribs[] = 
	{
		NSOpenGLPFANoRecovery,
		NSOpenGLPFAWindow,
		NSOpenGLPFAAccelerated,
		NSOpenGLPFADoubleBuffer,
		NSOpenGLPFAColorSize, 24,
		NSOpenGLPFAAlphaSize, 8,
		NSOpenGLPFADepthSize, 24,
		NSOpenGLPFAStencilSize, 8,
		NSOpenGLPFAAccumSize, 0,
		NSOpenGLPFASampleBuffers, 1,
		NSOpenGLPFASamples,4,
		0,0
	};

	NSOpenGLPixelFormat* fmt = 
		[[NSOpenGLPixelFormat alloc] initWithAttributes: (NSOpenGLPixelFormatAttribute*) attribs]; 
		
	return self = [super initWithFrame:frame pixelFormat: [fmt autorelease]];
}


And here's my toggler:

    

- (void) setAntialias: (BOOL) yesno
{
	antialias = yesno;
	if (antialias) glEnable( GL_MULTISAMPLE_ARB );
	else glDisable( GL_MULTISAMPLE_ARB );
}



Now, I know my toggle's being called ( the controller has the IBOutlet and forwards the message to the above method ). 

Thinking that I might need to try a different GL_MULTISAMPLE_XXX I grepped the headers and found that the glext.h header defines GL_MULTISAMPLE in various flavors, but all are #defines of the same constant. I also found that there aren't any other multisample constants, so there's nothing else to try.

What am I missing here? Do I need to kill the context and create a new one? The NeHE sample code just used glEnable/glDisable calls like I did above.

--ShamylZakariya

Where is the toggle being called from?  Usually, it is registered as a callback with one of the main routines.
Connecting to a Cocoa control may not be the same.

Bruce

I'm not a Cocoa noob ;) I know it's being called ( The oulet's called from the menu, does some logic to turn on/off a checkmark in that menu, and then calls this method. I also NSLog'd the above method call earlier, and I know it's being called ) -- the problem is the glEnable/glDisable don't do what they're supposed to do.

Yes, not technically a Cocoa question, but it seems to be an OS X question, since this code works on other platforms, supposedly.

--ShamylZakariya

----

See http://developer.apple.com/qa/qa2001/qa1268.html

Note the following two observations: 

"Regardless of the enabled state, rendering always goes through the multi-sample buffer, if one was created with the pixel format. When multi-sample is disabled, all coverage values will be set to one giving the appearance of non-multi-sample rendering. If the correct buffers have not been created in the pixel format, enabling multi-sample will do nothing."

"Note: Currently, ATI graphics hardware prior to the Radeon 9800 Pro and Radeon 9600 Pro do not support the enable flag for multi-sampling, leaving it enabled in all cases. It is recommended that client correctly set the enable flag for the context as needed but not expect glDisable to be respected for certain ATI hardware."

Any questions?

-- MikeTrent

I think you might have misread me Mike, I couldn't turn AA *off* -- e.g., it was always on. * ("leaving it enabled in all cases." -- MikeTrent) *

Anyway, I've figured it out. What you have to do is instead of glEnable/glDisable, you have to create a new pixel format, then a new context using it. You then have to make the view take the new context and make the new context take the view -- then you have to make it the current context and redraw.

My warning, to anybody who wants to make this happen, is that when you make a new context, you'll automatically lose your display lists and texture maps, so if you want to keep them you'll have to make the two contexts share eachother ( which is done through the initializer ).

--ShamylZakariya

 * ("leaving it enabled in all cases.") *

Hmmm.... I did see that, just I've got an NVIDIA card so I disregarded it. Anyway, my code's got fallbacks for cards which don't support multisample, and it's tested well, so as far as I can tell, problem's solved. --ShamylZakariya

Hi,

I am trying your trick to replace the broken glEnable(GL_MUTISAMPLE_ARB);/glDisable(GL_MULTISAMPLE_ARB); but it doesn't work. I created a second context which is shared with the first one. The second context has a different pixel format which includes AGL_SAMPLES and AGL_SAMPLE_BUFFERS_ARB flags. Although I get no error while creating these two contexts and even while setting them current, rendering doesn't occur if the shared context was created. If only one context is created everything works fine, including FSAA. I have a new Intel Dual Core 2 with Intel GMA 950 graphics card...

-Olivier

