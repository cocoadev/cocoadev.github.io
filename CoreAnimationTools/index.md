---
layout: page
title: CoreAnimationTools
---



Are there any tools for developing Core Animations?  I'm relatively new to Objective-C, but in my mind developing an animation 100% in code and not having any kind of GUI to preview or at least assist in the development of animations seems like it would involve a very steep learning curve.  Are there any tools to simplify this process?  If not, what about good resources for samples/demos?  I learn best by seeing examples, so any sites with more examples than just the 2 that come with XCode would be greatly appreciated.  Or good books (if they exist yet on the subject?)

Thank you.

----

Interface builder now includes options to set up transitions and filters.

----

Well, since making a transition is as easy as someView layer] setOpacity:0.0]; [[someView animator] setFrame:[[NSMakeFrame(...)];, it is not a problem of ease of use, but more of "what are you going to do with it"?
Although a Flash-like way to pre-specify animations could be nice (the same way we can use Quartz Composer to precompose visual effects). -- EmanueleVulcano aka millenomi

----

Thanks for the hint on the Interface Builder part.  That will help a bit, but I'm specifically looking for simpler ways to do more complicated and more 3-D like animations, such as the demo they showed when they first announced CA, where they had the iTunes screen saver that they made into a CA script using 1/10th the code.  Even a way to just "debug"/"step-through" an animation would be awesome.  I'll just keep playing around with it and see where. it goes

----
My understanding of CoreAnimation is that it is not 3D, it is more 2.5D in that it allows you to move lots of billboards around. I was also hoping to find that iTunes screen saver in sample code, and in the vein it would be beneficial if people could post links to known sample code here.

----
CoreAnimation is really just a way of organizing surfaces (views) in a tree.  You can apply perspective transforms to a layer (and/or its sublayers) for 3-D effects.  It's not 3-D in the sense that you can render meshes or full scenes; that's why you have NSOpenGLView.

----
I agree that it would be useful for people completely new to Core Animation to have an application that they could visualize the animations in. I usually just write a quick testbed application for that, but I imagine someone will create something like this soon.

----
From http://www.macscoop.com/articles/2006/12/19/leopard-build-9A321-all-the-new-features-listed  "For developers, Apple now provides a great example for the CoreAnimation API, it's actually the iPod nano ad made with iTunes covers."  Anyone actually seen this code?

----
I think that example is simply a visual example of what can be done with it, rather than a code example showing how to do it.  Unless someone else can show/prove me otherwise?  That code would be a great step towards what I am looking for (I personally learn best by example).
----

thats really NOT what core animation is for. Its actually exceedingly difficult to get core animation to do "real" 3d animation. core animation expects a beginning and an end key frame, and it fills in the middle. it doesn't really want keys in the middle. In addition it treats everything as separate entities. each one is 3d, but they are composited together in 2d, so everything is actually flat when it comes to layering depth. core animation is good for lightweight (and beautiful, btw) transitions for your UI, but not as a tool box for animators.

