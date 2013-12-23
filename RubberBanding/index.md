---
layout: page
title: RubberBanding
---

I've tried to find a tutorial describing how to do rubber-banding using Cocoa but havn't found any. Do you know where I can find one? It's mentioned in the documentation for NSBezierPath but not explained.

Thanks,
Gabbe

----

You might look at the Sketch example installed by the Developer CD. In Jaguar it lives here: 

        /Developer/Examples/AppKit/Sketch


It doesn't do marching ants (which would be sooo retro), but it does a simple selection rectangle. Change the selection color to light grey, and draw the interior of the box w/ a generous amount of alpha and you'll probably get something close to the Finder.

I'm not sure about simple examples, though.

-- MikeTrent

----

Oh, eh, maybe I've misunderstood what rubber-banding is. I thought it was something like what Golive used to make links to pictures etc. You could drag a rubberband like line from a point to anywhere else on the screen to connect two things to eashother. In Golive it was used to choose where a link should go and such. 

I guess my question should be rephrased to something like: How do I draw outside of a NSView (and outside of the current avtive window)? What I would like to do is to drag a line from a window to any point on the screen. I know how to do what I want inside of a NSView but how do I do drawing that is not confined to a NSView (if it is at all possible)?

/Gabbe

----

That isn't rubber-banding either. Rubber-banding is taking a set of points and finding the minimum polygon for that set of points, with certain criteria for the polygon (like, must be convex, or must not have an angle greater than thus, or whatever). Like when you put nails on a board and put rubber bands around them as a kid. I'm sure everyone did. I know I did, although I'll be damned if I can remember why I did.

For what you're doing though, it's kind of tricky. You aren't supposed to draw outside the view bounds (in fact, you can't!). Remember the Frame vs. Bounds thing? You draw in the coordinate space defined by the bounds, and extra stuff will be clipped.

I was thinking about how they did this.. and thinking about how to do lots of these things (like the OmniWeb address widget with history) must be a new window they create on the fly. That would be the easiest way, and you could make the window itself grow with the dragging, and still be able to use dragging-entered and stuff.

I know that's how the old Mail.app did its about screen, which is a slightly more jovial example of this idea.

-- DaveFayram

----

That sounds like what InterfaceBuilder does for connecting things to outlets. I seem to recall someone on some list saying it was done with two windows, one vertical, one horizontal. -- RobRix

----

Aah, thanks, I'll try using that technique. I'll post a example here if/when I get it to work.

/Gabbe

