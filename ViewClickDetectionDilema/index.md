---
layout: page
title: ViewClickDetectionDilema
---

I'm working on some silly little app that draws text in random spots in a subclass of General/NSView
the number of strings drawn to the view could be come huge...

I'm pondering how to detect if any of those strings are clicked on... I know I can get their location & drawn size, but when I go to check if they get clicked on, I only can think of the General/NSView subclass picked up a mouseDown and checking of the mouse click location is within any of the string's General/NSRects...

anybody know of a better solution than looping thru a large array of objects with strings that have to be drawn to the view and checking if the point is within any of them??

any help is appreciated, still not very knowledgeable about Cocoa yet :)

 -- General/JeremyK

If you want this to work quickly, you're going to have to do something more *elegant*. 

My personal approach would be to make a hashing type system where your strings would register themselves as being in a certain quadrant ( or more than one if there's overlap ) of the view ( as in upper left, upper right, lower left, lower right ). Then each of these quadrants might have it's own hash for sub quadrants. And so on recursively with the limit being some reasonable size larger than a pixel ( obviously ) : i'd say something like a square 50 pixels across. Then, when the mouse is moved/clicked/etc, you can *easily* determine the quadrant and thereby limit your search space to the strings in that quadrant or overlapping it. 

Easy as pie.

P.S. I think this is something like how 3d games cull their graphics pipeline to only consider drawing those things in a certain active space without having to iterate over every object in the level.

--General/ShamylZakariya


What about the possibility of drawing the strings in their own General/NSView's the size of the text drawn, therefore they each can have a click call associated with some controller...

would this be too excessive?  I was pondering doing a split method like you mentioned and will be on my list of possibilities to consider :)

 -- General/JeremyK

Presumably the views can overlap, and Cocoa doesn't handle overlapping sibling views very well (AFAIK, it's undefined what order they get drawn in is the main reason)  Not to mention that focusing a view is pretty heavy-weight.   Also, the value of "huge" should be quantified.  If Huge is a couple hundred strings, then doing the simple linear search through the string bounding rectangles (which you can calculate once and store in an array) on a click probably is no big deal.  If you have tens of thousands of strings, then something more sophisticated for the hit detection is most likely in order (but then drawing that many strings may prove to be the bottleneck).  I personally would implement it the most straight-forward way to start out with to a) make sure the program's idea works (no reason to invest a lot of time in a complex scheme if the fundamental idea is flawed, or you decide to do something different) and b) make sure you really have a problem.  If performance is OK, don't worry about it.  If performance is a problem, then measure your program using Sampler or Shark and see where the actual bottlenecks are.  Sometimes it can be surprising.  ++General/MarkDalrymple


Thanks, will explore and see how it goes :)

-- General/JeremyK

Excellent point Mark. I just got excited to make up a quadtree like searchspace for 2d graphics. I've done this before, when I was ( long ago ) developing a subpixel precise vector drawing API for General/BeOS. 

But anyway, yes... PROFILE, PROFILE, PROFILE. then, PROFILE again.

--General/ShamylZakariya
