---
layout: page
title: ScreenSaverWithPicture
---

I'm trying to write a screensaver.

I want the screensaver to be set to an image and sometime later I want it to change that image to a newly created image.  

I can create the necessary images, but not when I'm making the screensaver. They will be created in real time.

----

Maybe you should start with a *much* simpler project, because despite having never written a screensaver myself, just a quick look at the ScreenSaver framework makes it very clear what to do. I'd always seen this site as more as a repository of general knowledge, and not so much specific questions (like "how do I make my button open a dialog box").  

----

I think the simplest way to do it would be to keep an array of image representations and draw them whenever you want. I'm not sure if it's the most "efficient" way to do it, though. Yet, if you have lots of pictures, you may bump into memory problems.

    
NSArray *myReps = [[NSArray alloc] initWithObjects:
[NSImageRep imageRepWithContentsOfFile:[[NSBundle bundleForClass:[self class]]
                        pathForResource:@"image1" ofType:@"tiff"]],
[NSImageRep imageRepWithContentsOfFile:[[NSBundle bundleForClass:[self class]]
                        pathForResource:@"image2" ofType:@"tiff"]],
etc...
,nil];


Of course, if you have many pictures, you'll probably want to make a loop...
You draw the picture using something like:

    
myReps objectAtIndex:n] drawAtPoint:[[NSMakePoint(x,y)];

I know they are not created in real-time, but maybe it's because I don't figure why you would create them during runtime... Yet, you could always add the representations to the array only when the time comes... In this case, you should use NSMutableArray...

-- Trax
----

An even easier way would be to use     -[NSImage imageNamed:].  That will look through your bundle and load your image.  Have a loop at the start of your screensaver to load your images.  Stick them into an NSArray. Then when it is time to change the image, get the next image out of the array (keep the index of the currently displayed picture around somewhere so that you know what image to get next)

----

None of these things address what the OP is asking about. OP asks how to write a screen saver that draws an image, and later replaces the first image with a second image. Images are generated in real time. So caching images in an NSArray isn't what the user is asking about; also since the images are generated in real time, imageNamed: isn't appropriate either.

I can summarize the solution, but I don't have time to write the solution in detail.

1) Create a ScreenSaverView subclass. Override drawRect:(NSRect*) to composite the contents of an image on screen. Set the animation rate to -1, so the repeating timer never fires.

2) Start generating a new image. You might do that in -[ScreenSaverView animateOneFrame] (if generating the image is fast) or you might do that in a background thread (if generating the image is slow). More complicated solutions exist (where you generate pieces of an image in individual calls to animateOneFrame. I've written many modules that do that.). If you are using animateOneFrame, you'll need to set the animation rate to some positive value in step 1.

3) Once the new image is generated, call -[ScreenSaverView setNeedsDisplay:] with an argument of YES. This will cause the new image to be drawn. 

See the discussion on ScreenSaver for more information on writing a screen saver module. This is actually quite easy. Read up on the ScreenSaver API , NSImage, and NSBitmapImageRep. When you have more specific questions, ask away. Or maybe someone here will be able to post a more complete solution.

-- MikeTrent

