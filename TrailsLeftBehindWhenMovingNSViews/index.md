---
layout: page
title: TrailsLeftBehindWhenMovingNSViews
---



If I move an General/NSView programatically (say I have a ball I made by doing an oval in the bounds of the General/NSView subclass, and I move it like this)

    

        General/NSRect frame = [ball frame];
        frame.origin.x++;
        [ball setFrameOrigin: frame.origin];
        [ball setNeedsDisplay:YES];



This works, but it leaves behind a little trail of the color of the oval drawn.  Why?  How can I fix this?

Thanks

--General/CharlieMiller

----

You need to set the area you've vacated as needing display too. -- Bo

----

Thanks.  This worked.

    
 General/window contentView]setNeedsDisplay:YES];

--[[CharlieMiller

----

That's a little like squashing a fly with a piano.  It's probably better to just save the old frame and call General/self superView] setNeedsDisplayInRect:[[NSUnionRect(oldFrame, frame)];  -- Bo

----

Ah, allright.  How about this one then, I'm trying to tell where the farthest right part of the window is, by getting the width of the window, but if I ask for the width of [window frame] (window, by the way, is just a reference to the window), it says the width is 0.  Odd.  Thanks.

--General/CharlieMiller

----

Dunno.  Are you perhaps trying to read it into an int instead of a float?

----

Yep.  (Smacks self in the forehead).  Things work now.

----

I make that mistake at least weekly and usually much more.  I really wish there was a compiler warning for this considering that it doesn't even convert between floats and ints in a mathematically correct fashion unless you coerce it.  -- Bo

Good lord, that's horrible. But yeah, I've run into that myself... one of my personal prides is that before Jaguar was released, I found a bug in GLUT which led to timers being all screwed due to a conversion between double and int gone awry. -- General/RobRix
