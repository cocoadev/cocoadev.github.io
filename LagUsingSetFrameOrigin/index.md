---
layout: page
title: LagUsingSetFrameOrigin
---

I have created Sticky panels.  All is well, except that when I move the "leader", the "followers" don't follow all that fast.

Basically, the code that does the move looks like this, where thePanel contains the view for which mouseDragged is implemented.  childPanel is "Sticyk" to thePanel.
    
- (void)mouseDragged:(NSEvent*)theEvent
{
	customPanel *childPanel = [thePanel childPanel];
	
	NSPoint origin = [thePanel frame].origin;
// Block 1
	origin.x += [theEvent deltaX];
	origin.y -= [theEvent deltaY];
	[thePanel setFrameOrigin:origin];
// Block 2
	origin = [childPanel frame].origin;
	origin.x += [theEvent deltaX];
	origin.y -= [theEvent deltaY];
	[childPanel setFrameOrigin:origin];
	[childPanel orderFront:self] ;
}


This works, but the childPanel "lags" during movement.  If I swap block 1 and block 2, then the "parent" (thePanel) lags.  It would appear that the first setFrameOrigin moves the window and draws it, but the next setFrameOrigin only happens some time in the future.  Is there a way to batch the two together, so the windows move as though they are one?

----
If you want two windows to stick together, there's an API for this. Look at NSWindow's     -addChildWindow:ordered: method. If this isn't an option for some reason, you can coalesce updates to different windows by using NSDisableScreenUpdates/NSEnableScreenUpdates.

----
Thank you for the reply.  This -addChildWondow:ordered: method worked, only as long as the "chain" was 3 windows or less.  By chain, I mean 1 adds child 2, 2 adds 3 and 3 adds 4.  If I then move 1, 4 would not move.  It would stay ordered above (I use NSWindowAbove) but wouldn't move.  What would happen, is that 4 would appear to be "torn off" from the other three.  If I then disconnected 3 and moved it, 4 would move with it (snapping back to its bottom, where it was connected).

For me, I thus needed both the suggested methods.  I would addWindow the child so that the ordering would work, but I would still have to recursively move 2, 3 & 4 when 1 moved, all wrapped in the NS[Enable|Disable]ScreenUpdates.  The last thing I found strange was that I needed to "rewire" the chain when I broke it in the middle, or the ordering would be off.  So, when I moved 3, to disconnect it from 2, I had to also disconnect 4 from 3 and reconnect it (that's what I do, I guess maybe you could reorder it too?), ordered NSWindowAbove or you would suddenly find 4 above 3.  I guess calling removeChild on 3 (from 2) causes 3's ordering to change, even relative to it's own children.

