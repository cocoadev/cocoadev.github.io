---
layout: page
title: UnSetNSShadow
---

**F**'orcingWindowShadowShape** 

I have a semi-transparent window, but it is a rectangle... and it contains animation.. whenever i resize it, the window automatically calculates the new shadow shape based on the current animation frame, which is not what i want (it leaves a mess!), i want the shadow to appear around the transparent window (as if its not transparent). Is there a way i can tell it to just use the rectangle shape and stop trying to be smart and calculate things?

----
Can't quite tell from what you wrote if the window is actually transparent or has a color with a alpha value > 0.0 but < 1.0 (semi-transparent). If your background has an alpha of 0.0, have you tried using a background color with an alpha of something like 0.01?  Just a thought. 

Calling     [window invalidateShadow] in some suitable     windowDidResize delegate message might do something

----

It's semi-transparent, has a color with an alpha value between 0 and 1. Calling invalidateShadow doesn't help because the shadow is made around the shape of the picture in the window, rather than the window rectangle :-(

----
I'd try using a separate [child] window behind the semi-transparent window:     [window setHasShadow:NO] then create a new window with the same dimensions as 'window' and     [window addChildWindow:newWin ordered:General/NSWindowBelow]

----

Wouldn't this defeat the purpose of the window being transparent? :P

*Couldn't you make it mostly transparent but draw just enough (like a light colored General/NSFrameRect()) for the window to base a shadow off of?*

----

If your window is borderless (no title bar), then you could set it to not have a shadow at all, and then draw your own. Make your window larger than is needed by your drawing, and draw your own shadow wherever you want it. If you can require 10.3, General/NSShadow should make this fairly easy. If not, it's still possible with CG calls, although harder, I'm sure.

----

If it's only during resize, you can stop the animation when resizing starts and/or make the window opaque, and restore the previous state with the new size.

----

**General/UnSetNSShadow**

I have a custom General/NSView subclass, and I'm drawing something in there and I give it a nice General/NSShadow (supported under 10.3+). Great, very easy, just use the General/[NSShadow set] method.

I have lots to draw on top of this object that I *don't* want to have this shadow. The documentation mentions refreshing the General/GState, so after drawing all my shadowed stuff I call [self renewGState];

This doesn't seem to have an effect. I'm sure there must be a way to stop drawing with a shadow. I've tried Googling but no luck... and I'm pretty sure there's no General/[NSShadow unSet] method :)

----

I think what you want to do is something like this:
    
General/[NSGraphicsContext saveGraphicsState];
[shadow set];
...draw shadowed stuff...
General/[NSGraphicsContext restoreGraphicsState];
...draw nonshadowed stuff...

I've never tried it, though, so I'm not sure.

----

**Resolved**

That works perfectly, thanks so much for your help!
