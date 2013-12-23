---
layout: page
title: NSImageViewMemoryLeak
---

I have a program that displays a series of images using NSImageView.  The memory used by each image is not released and eventually swap space runs out.  Here is my code snippit:

    
IBOutlet NSImageView *imageView;

NSImage* image = [[NSImage alloc] initWithContentsOfFile: fileName];
[imageView setImage: image];
[image release];
[imageView display];


I'm running 10.3.1

Any ideas?

Oh, one other problem with     [imageView display]; for some bad images I get a bus error and my app crashes.  Is there any way around this?  BTW, Preview.app crashes in the same manner.  It seems that Apple is indexing into unallocated memory because a read did not succeed because the image file (.tif or .jpg) was truncated.

----

try

    
NSImage* image = [[[NSImage alloc] initWithContentsOfFile: fileName] autorelease];
[imageView setImage: image];
[imageView setNeedsDisplay:YES];


and see what happens. If you still have a memory leak than I think it's safe to say that the leak is somewhere else. 

----

It does not seem that way, commenting out the NSImageView stuff results in no noticable leak.  With the NSImageView code I am leaking about 1GB/minute.

----

I think you are not giving the autorelease pool a chance to cycle and do it's thing. Is this code being called from a loop?

----

I call RunModal.

But, the real problem is NSImage.  This leaks:

    
image = [[NSImage alloc] initWithContentsOfFile: fileName];
[image release];


and I am calling:
		[mApplication runModalSession:mModalSession];
between calls to the subroutine that has the above code in it.  In fact that is all the code in the routine.  If I comment out the above lines I don't leak.  With them in I leak about 15MB per call.

----

First question, why are you running the window modal?

Second question, is the     beginModalSessionForWindow: balanced by an     endModalSession:?

----

First: Because I have a progress indicator and need to update the GUI.

Second: Yes.

I found the problem but do not have the solution.  Evidently NSImage is autoreleasing the memory and since I an not going back to the run loop it never gets released.

----

In that case, the solution is to manually create your own autorelease pool.   The template is:
    
- (void)myLoopedMethod
{
	// when you create one, it's automatically made the current pool.
	NSAutoreleasePool *thePool = [[NSAutoreleasePool alloc] init];  
	
	// your code that needs stuff autoreleased outside of the main run loop goes here

	// when the pool is deallocated, all autoreleased objects are released and the previous pool is made the current pool.
	[thePool release]; 
}

Best of luck.  -- Bo


----

Yes, that is the trick.  Thanks to all.

