---
layout: page
title: SpotlightSystemPreferencesGrayOutEffect
---



I want to recreate the gray-out effect in Tiger's System Preferences where typing a term in the System Preferences search bar highlights matching panels while graying out the rest.

Hoping someone blogged an opinion or even some sample code or discussed it in the past, I googled everywhere. I tried "spotlight system preferences", "spotlight gray out system preferences", "core image gray out", "core image gray out", and many variations thereof.

How does one draw this? I can't think of an easy way with bezier paths or the geometry functions, since my first approach would be to try to describe a rectangle by subtracting the rectangles of the matching results, filling the remainder. I have no idea how to accomplish this with either NSBezierPath or the geometry functions.

Is there some obvious approach I'm missing? Maybe a not-so-obvious one? I'd appreciate any possible approaches and / or a good reference.

----

Doesn't CoreGraphics support LayerMasks, or drawing with Masks??

----

ClassDump would be a good place to start looking. Take a gander at SpotlightView.

-- Falco

----

Thanks to the first responder for the CoreGraphics suggestion. I'll check that out. To Falco, the class-dump doesn't tell me anything at all about how the view is actually drawn. It only tells me the names of the methods that particular class uses (like -addSpotlightWithCenter: which tells me what one would already assume - it keeps track of an array of points for the center of the "spotlights" in the view). This is far removed from the actual drawing code, which is what I need.

----

True, but it does tell you that they apparently aren't using anything special to do this. It seems that they are simply adding a view over the content of the window. Then one would guess that it uses standard animation and gradient drawing to actually achieve effects. If I were recreating it, this is how I would do it, and because with some of the CG gradient wrappers out there, this could be done quick and easily without having to dig into the low level CoreGraphics stuff unless absolutely necessary.

-- Falco

----

Falco - I had already guessed it wasn't anything special that is used to achieve this effect. Again, that doesn't answer the question ... *how*? If CoreGraphics is overkill, then perhaps a reference to an article or a CG gradient wrapper you know of would be a useful reply, rather than reiterating your earlier suggestion that I should be able to figure it out by looking at it. If you have no specific suggestions, this round goes to the original responder -- CoreGraphics is the way to go.

