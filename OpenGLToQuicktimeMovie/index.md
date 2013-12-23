---
layout: page
title: OpenGLToQuicktimeMovie
---

Hi everybody,
I'm interested in help as to how I might take a number of saved out frames from OpenGL and write them to a quickime movie. 

My half-baked plan is this:

 1) Write out each open gl frame in some fast manner to disk, say, raw bitmaps 
     to /tmp/somefolder/0.ppm 1.ppm, 2.ppm and so on

 2) When recording is done, for each saved frame, I convert it to something 
     quicktime likes and add it to a "movie" object, somehow

 3) let quicktime compile/compress/whatever and save the movie to disk.

 4) Delete the raw bitmaps from /tmp


I can do step one, I think -- I have a well read copy of "The Red Book" and understand, in principle, how to read framebuffer data. I'm comformtable with OpenGL. Step 4 is easy. It's the magic of steps 2 & 3 that bother me.

If anybody has done anything like this, I'd really appreciate some pointers! I've read up on the Quicktime sample code in developer.apple.com and found nothing to my benefit. Oreilly has had some Quicktime for Java articles that go into movie creation, but my program is Cocoa/c++ and I'm not certain that Java is feasible.

Should it come to it, I might write a sub-program in java that builds a quicktime movie from the frames in /tmp -- but then, aren't there command-line programs that do this already?

I'm very new to this topic, so any help would be deeply appreciated.

--ShamylZakariya

http://developer.apple.com/samplecode/Sample_Code/QuickTime/Basics/CocoaCreateMovie.htm --zootbobbalu

I'll be damned. How did I miss that? Thanks! (I really did look at the QuickTime sample code...)
--Shamyl Zakariya

----

I'd point out you can probably skip the trip to NSImage completely and go from OpenGL pixels directly into a GWorld (maybe by blitting the raw pixel data yourself?). Then you won't have to monkey around with NSBitmapImageRep, you can use a fixed number of memory buffers (a big GL pixel buffer, and a big GWorld), you can avoid touching the filesystem, and generally streamline your performance. So:

1) Get the raw OpenGL frame

2) Convert to GWorld

3) Compress and insert into Movie

4) Go to 1 as long as you still have frames...

-- MikeTrent

