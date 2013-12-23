---
layout: page
title: NSBezierPathSurroundingPoints
---



Hi there. I'm one of the guys working on Pixen (http://www.opensword.org/pixen), and we're having a bit of trouble with an efficient algorithm for drawing a selection marquee. Basically, we have a bitmask of the entire canvas for selected vs. nonselected, and various tools modify it as is appropriate. Then when it comes time to draw, if there's any selection, we draw a selection marquee that covers every pixel that's on in that mask. The problem is that we can have irregular selections - multiple selections added together, lassos, magic wanded areas, and so on, so we can't really use any general algorithm (like just storing selection as an NSRect and drawing a path around that).

The algorithm we're using to generate it right now is really ugly. Basically, it goes through and asks every point what the point on each side of it is like. For instance, on point a, if the point above it is unselected, we know we need a line on the top edge of point a. This is, however, immensely slow, and since it's done for every pixel, it creates an absurd number of control points. I won't paste the entire method into this page (it's really huge and ugly), but if any of you would like to see it, it's available at http://www.andymatuschak.org/websvn/filedetails.php?repname=Open%20Sword%20SVN&path=/Pixen/trunk/PXCanvasView.m&rev=0&sc=1, about halfway down the page (- drawSelectionMarqueeWithRect:). Does anyone have any suggestions on how to improve this? I've looked into convex hull algorithms, but they're not really efficient since every point inside the selected area is on as opposed to just a few.

A second problem is that the line dashing doesn't carry over intelligently between bezier elements, so if we do a marching ants style of selection, it just draws as all white, since each pixel is a separate line element.

-- AndyMatuschak

My current code goes like this:
Loop over the mask, first vertically, then horizontally.
If you find an edge pixel, note its position, then inner-loop until you find one that isn't on the edge. Add the resulting line to the bezier-path.

Draw the resulting bezier path once, with the drawing color being a stripe pattern. You can set the offset of the pattern according to the current time.
You'll have to draw without anti-aliasing (looks better and is way faster).
For the bezier path functions, use IMP caching; needless to say, you cache the resulting bezier path, invalidating only on mask changes.
I was able to get my code to scan 30.000 kiloPixels per second with this method (on an iBook 600); this seems like a realistic number to achieve, given that PaintShopPro doesn't seem noticably faster on an Athlon 750.
I don't have Photoshop, so I don't know its performance re this task, but GIMP performs noticably worse.

Of course, this is essentially brute-force; but it's really interesting how it's possible to optimize such an algorithm: my first try pegged out at 3.000 kpx/s.
I'd be really interested in how others solve this task...

-- JohannesFortmann

----

Using a path strikes me as inherently inefficient for lasso-style or otherwise arbitrary selections. Instead, perform an edge detection on your mask, then draw the old-style marching ants where the edges are. Alternatively, for a modern finder-like selection, create an overlay window, draw your selection mask into it translucently, and use the edge-detected version to draw an outline. Either way, use bitmap-oriented algorithms, since your selection mask is a bitmap.

----

Err, how would I draw the "old-style marching ants"? I'm new to Mac programming, so I really don't know.
I don't believe there's a method that's faster than using a bezier path, since the drawing is quite optimized for the respective system. The amount to draw doesn't vary anyway, and the performance problem (at least in my app, I sharked) is definitely not the drawing of the path (which is cached in a bitmap anyway...).

On performing edge detection: that would still involve a scan of the whole image, since you've got to find the edges in the first place (which is the slow pass in my code). Also, using vImage with an edge-detect kernel (while possiblly faster than my hand-written code) would yield an image that would be totally unusable (the edges are not necessarily sharp or anything, since the mask could be fuzzy...

I'd love to stand corrected (if possible, with examples :-)
-- JohannesFortmann

----

Heck, I'm not new to Mac programming, and I can't really think of a better Cocoa-y way to draw lines. I mean, yeah, there are always things one can do with the lower frameworks, but that's not really desirable.

-- AndyMatuschak

----

This isn't a very high-resolution bitmap we're talking about; the resolution is frequently 16x16 or lower.  We'd like the marching ants to be the same size, no matter the zoom, so doing edge-detection directly on bitmap data is out of the question.  Besides, as Johannes said, you have to scan every pixel to do edge detection.  So unless you used Core Image, it wouldn't be any faster than the bezier path method.

-- IanHenderson

----

It would have the same *time complexity*, but building an edge-detected bitmap is going to be a zillion times faster, in terms of a constant multiplier, than building a long and complicated bezier path.

----

Well, like I said, that doesn't really matter when the resulting bitmap is too chunky to be used for marching-ant purposes.  We need an algorithm that finds lines, not squares.

----

On a similar topic, Will Thimbleby suggested that we use a patterned color for the bezier path's stroke. The problem with that is that when we zoom in to about 1000%, a single pixel in the image (obviously) becomes ten pixels. We can't simply resize the image down, because at a certain scale, we'll lose data. Any suggestions on how to deal with this? We still need the affine transform around to scale the bezier path, we just don't want it to scale the pattern color.

-- AndyMatuschak

----

It would seem we have a working implementation!  Thanks for your help, everyone.  We're using our original algorithm, but only in the bounding rect for the selection, so it's faster.  We only calculate it once every time the selection is modified, and then it just caches the path.  Then we apply a constantly-animating pattern color to the path.

-- IanHenderson

----

The Quartz 2D programming guide suggests using an overlay window to provide feedback on a selection.  It also hints that marching-ants are now antiquated.  Probably shaded highlights (ala Preview and iPhoto) are the currently recommended approach.

http://developer.apple.com/documentation/Carbon/Conceptual/QuickDrawToQuartz2D/tq_regions/chapter_8_section_3.html#//apple_ref/doc/uid/TP40001098-CH222-CJBDHEFD

**Interesting, does anyone know if there is a guide on overlay windows for Cocoa? Do I just put a transparent window overtop my content window? How do I make sure I am drawing in the right place? When I Implemented overlay window for a sound editor I wrote for 10.1, I found that the windows got out of sync, probably from my own inadequate programming, and ditched the idea (there was no quartz extreme at the time). Now looks like a good time to rethink the idea.** -Jeremy Jurksztowicz

Not quite sure what you're asking, but one useful tip is to make the transparent window a child window (    -[NSWindow addChildWindow:ordered:] of the primary window.  A parent window drags a child with it whenever it moves, so it's easy to keep them in the same relative position.  Is that what you mean by 'windows out of sync'?

----

Yes, we did shaded highlights at first but then decided that they were suboptimal for an image editing program (those really don't count, as you're not painting) since they distort the colors inside the selection.

*Preview and iPhoto don't distort the colors inside the selection, the shade the parts of the image outside of the selection.  Also, the guidelines are specifically talking about image editing programs (which is not to say that I think you should necessarily change, that's your decision).*

