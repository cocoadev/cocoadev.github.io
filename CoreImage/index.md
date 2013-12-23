---
layout: page
title: CoreImage
---

Core Image is Apple's new hardware accelerated Framework for creating and using filters, effects and transitions.

More information:
http://developer.apple.com/macosx/coreimage.html

----

Just put some sample code up at http://www.macs.hw.ac.uk/~rpointon/osx/coreimage.html

It shows how to do some core image transitions, with the help of NSAnimation. The transition is between two NSView.

Also shows some transparency and shadow tricks.

- RbrtPntn

----

How do I render a core image to the screen without sucking it back from the video card into main memory first?  The above code begins
to suffer in performance for large windows because it renders the core image as part of an NSView within a NSWindow. I'm missing
something obvious here...

----

You need to create a CIContext from an NSOpenGLView and just draw directly inside the drawRect call. Check Apple's example named: CIExposureSample.

-- Jacques Lema

----

Does anybody know how to programmatically check if the hardware an application is running on supports Core Image hardware acceleration or not? Is there a certain OpenGL extension that must be present for Core Image hardware acceleration to work?

----

You need to check for a card that supports: 'GL_ARB_fragment_program'

Here what I use in ChocoFlop to detect this:

    
self openGLContext] makeCurrentContext];
const [[GLubyte* strExt = glGetString (GL_EXTENSIONS);
const GLubyte* extname = (GLubyte*) "GL_ARB_fragment_program";
pixelShadersSupported  = (BOOL)gluCheckExtension(extname, strExt);


I believe the Nvidia 4200Go in G4 Powerbook 12' machines support this but is slower than CPU-based operations. Apple's implementation automatically disables GPU hardware acceleration for this specific device.

-- Jacques Lema

Also check this: http://developer.apple.com/qa/qa2001/qa1218.html

----

I just published Flipr, sample code to do widget-like window flipping - it's a category on NSWindow, and uses CoreImage and NSAnimation.

Details and downloads here: http://www.brockerhoff.net/src/index.html

*RainerBrockerhoff*

----
Flipping windows with CoreImage is rather wasteful. You're better off flipping them with CGSSetWindowWarp. You'll get 60fps on most machines and there's no messy window or shadow redrawing to deal with.

--- KelvinNishikawa --

----
I believe the objective was to avoid undocumented APIs.  However, can these window effects now be all done by CoreAnimation?

