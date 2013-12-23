---
layout: page
title: EnteringOrExitingTrackingRectsCausesSlightDrawingGlitches
---

I have an NSView custom subclass with several subviews of a certain type, they in turn have several subviews of their own, and everything works, except that whenever I mouse enter a tracking rect (all of the aforementioned objects have tracking rects) the other subViews flicker a bit and their stroke outlines (0.5px) become slightly darker.

I have tried to setNeedsDisplay on everything even after every mouse entered event, but no luck it seems. What's happening is hard to explain clearly.

Is there an easy explanation for this effect, or is it quite an isolated thing I'm going to have to spend a fair bit of time rooting out?

----

While I couldn't tell you exactly why the problem is occurring, the darkening of the outline is symptomatic of multiple redraws without a full clear. You might experiment with having your views return YES for      -(BOOL) isOpaque;  but that causes problems in and of itself if your overall display isn't over, for example, a solid white background.

You might give a shot at something like triggering a redraw for the master root-view ONLY, and not manually redrawing the subviews but instead letting Quartz redraw them as it sees fit.

For what it's worth, I've experienced this and I solved it by making my views opaque and having the master view have a white background. In my circumstances, a white background made sense. It won't always, however...

--ShamylZakariya

----

Thanks! Turns out I just have to call setNeedsDisplay:YES to the root view in a couple of important places rather than the subViews and the flickering outlines are gone... and I am very happy. Shamyl you've helped me out - and doubtless so many more - so much recently if you're at WWDC 2005 you must remind me to get you a beer or three :)

----

Well, if I ever make it there I'll call you out on it ;) Trouble is, I'm not a professional ( I'm an employed but poor web designer by day ) so unless money falls from the sky it's unlikely I'll make it to california, particularly since I'm all the way over east in Washington DC. 

I'm glad to help whenever I can. When I started learning Cocoa ( less than 2 years ago ) this site saved my butt many-a-time, so karmically I have to help in return...

--ShamylZakariya

