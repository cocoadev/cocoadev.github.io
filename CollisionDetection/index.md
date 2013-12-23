---
layout: page
title: CollisionDetection
---

Can somebody give me anything that can help me understand Collision Detection in OpenGL.
I've tried NEHE but I didn't understand it because it also used .cpp files wich I don't understand!
Anything but that will be great!

----

You should read up some on vectors and matrices. Check out google, should give you enough to get you started.
--AntonKiland

I'd like to mention, collision detection is ludicrously, impossibly, brain-bustingly non-trivial. There are many many papers out there on the heuristics for doing it efficiently. The obvious approach is to use bounding spheres for quick checks but then, for every step, you have n*n checks to make. You'll have to do temporal coherence trickery and other stuff to make it approach reasonable performance.

Also, it's not really the core topic of CocoaDev, though I'm sure we're happy to answer your implementation questions, should you choose to write it in Objective-C. Personally, I'd do it in C++ ( I have already, BTW ).

--ShamylZakariya

I did mine in ObjC. Although come to think of it it wasn't collision detection so much as picking, which while implemented similarly doesn't have the same semantics...

In any case, it was trivial to do picking with OpenGL; make sure all the objects are being named properly on the name stack, then when you want to start picking switch into select mode, set up the viewport to be small and centred around the mouse, and then from the list of objects drawn in there, select the closest one.

-- RobRix

Could you give a little more info on your picking technique, RobRix? I never really grokked the name stack. Anyway, I recently wrote a picker myself, but I took the approach of redrawing the scene in the backbuffer with each ( selectable )object having a unique (hashed) color; non-selectable objects were not drawn and the background was black. I then read the color under the mouse position and used that to determine which object was clicked. Of course, I then re-rendered in non-picking mode into the backbuffer to avoid flickering. It works for me. Meh. --ShamylZakariya

----

Further discussion (not related to packet collision) on this not-always-Cocoa-related topic can be found in

[Topic]

