---
layout: page
title: MagneticWindows
---

Has anyone gotten the magnetic window effect to work?  I was able to do it back in OS9 (MacToolBox, not even Carbon, but I'm sure it is the same...) but I haven't been able to accomplish this in Cocoa.  The problem, I've found, is that the WindowManager handles all window dragging, so there is no easy way to run the code.  NSWindow Delegate's method windowDidMove: doesn't work because that only gets called after a pause while dragging, not after every movement (which I suppose could be a desired effect, but my experience with this so far is that the results just aren't as cool as the real thing)

In case anyone is wondering.... MagneticWindows, or StickyWindows whatever you want to call it, is when you are dragging a window and it gets pulled toward nearby windows to help with alignment and stuff.

-JamesCallender

Adobe Illustrator and I think Photoshop still do this... and Fire can dock to screen edges, though I'm not sure if that's what you were looking for.

-- RobRix

It should be called MagneticWindows if they're metal TexturedWindows, and StickyWindows otherwise. Hope this helps. ;^)

-- AngelaBrett

*For non-metallic Aqua windows, they should be SurfaceTensionWindows!*

----
I've whipped up some simple "magnetic windows" code if anyone is interested:
 - (void)windowDidMove:(NSNotification *)notification
 {
 	NSEnumerator *e;
 	NSWindow *theWindow, *window;
 	NSRect frame, myFrame;
 	BOOL hDidChange = NO, vDidChange = NO;
 	float gravity = 10;
 	
 	theWindow = [notification object];
 	myFrame = [theWindow frame];
 	e = NSApp windows] objectEnumerator];
 	
 	if ([[NSApp currentEvent] modifierFlags] & NSAlternateKeyMask) return;
 	
 	//NSLog(@"%d", [[NSApp windows] count]);
 	
 	while (window = [e nextObject])
 	{
 		if (window != theWindow && [window isVisible])
 		{
 			frame = [window frame];
 			/* horizontal magnet */
 			//NSLog(@"%f %f", NSMinX(frame) - NSMinX(myFrame), NSMinY(frame) - NSMinY(myFrame));
 			if (!hDidChange && fabs(NSMinX(frame) - NSMinX(myFrame)) <= gravity)
 			{
 				//NSLog(@"NSMinX(frame) - NSMinX(myFrame)");
 				myFrame.origin.x = frame.origin.x;
 				hDidChange = YES;
 			}
 			if (!hDidChange && fabs(NSMinX(frame) - NSMaxX(myFrame)) <= gravity)
 			{
 				//NSLog(@"NSMinX(frame) - NSMaxX(myFrame)");
 				myFrame.origin.x += NSMinX(frame) - NSMaxX(myFrame);
 				hDidChange = YES;
 			}
 			if (!hDidChange && fabs(NSMaxX(frame) - NSMinX(myFrame)) <= gravity)
 			{
 				//NSLog(@"NSMaxX(frame) - NSMinX(myFrame)");
 				myFrame.origin.x = NSMaxX(frame);
 				hDidChange = YES;
 			}
 			if (!hDidChange && fabs(NSMaxX(frame) - NSMaxX(myFrame)) <= gravity)
 			{
 				//NSLog(@"NSMaxX(frame) - NSMaxX(myFrame)");
 				myFrame.origin.x += NSMaxX(frame) - NSMaxX(myFrame);
 				hDidChange = YES;
 			}
 			/* vertical magnet */
 			if (!vDidChange && fabs(NSMinY(frame) - NSMinY(myFrame)) <= gravity)
 			{
 				//NSLog(@"NSMinY(frame) - NSMinY(myFrame)");
 				myFrame.origin.y = frame.origin.y;
 				vDidChange = YES;
 			}
 			if (!vDidChange && fabs(NSMinY(frame) - NSMaxY(myFrame)) <= gravity)
 			{
 				//NSLog(@"NSMinY(frame) - NSMaxY(myFrame)");
 				myFrame.origin.y += NSMinY(frame) - NSMaxY(myFrame);
 				vDidChange = YES;
 			}
 			if (!vDidChange && fabs(NSMaxY(frame) - NSMinY(myFrame)) <= gravity)
 			{
 				//NSLog(@"NSMaxY(frame) - NSMinY(myFrame)");
 				myFrame.origin.y = NSMaxY(frame);
 				vDidChange = YES;
 			}
 			if (!vDidChange && fabs(NSMaxY(frame) - NSMaxY(myFrame)) <= gravity)
 			{
 				//NSLog(@"(NSMaxY(frame) - NSMaxY(myFrame)");
 				myFrame.origin.y += NSMaxY(frame) - NSMaxY(myFrame);
 				vDidChange = YES;
 			}
 		}
 		//if (v_isChanged && h_isChanged) break;
 	}
 	[theWindow setFrame:myFrame display:YES];
 }
Only slightly tested (should compile...) but seems to work ok.  Only problem is that due to the nature of the Window-Manager, this will only fire after you either stop dragging the window, or pause for a moment while dragging.  Of course, as I mentioned earlier, this could be a nice effect...  Enjoy!

-- [[JamesCallender

----

I modified the above code to snap to screen edges as well as other windows. Replace up to the     /*horizontal magnet*/ line in the code above with the following:


 - (void)windowDidMove:(NSNotification *)notification
 {
     NSEnumerator *e;
     NSWindow *theWindow;
     id edgeObject;
     NSArray *frames = NSApp windows] arrayByAddingObjectsFromArray:[NSScreen screens;
     //Edit 7 October 2004: this prefers window edges. change the above line to:
     //NSArray *frames = NSScreen screens] arrayByAddingObjectsFromArray:[NSApp windows;
     //to give snapping priority to screen edges.
 
     
     NSRect frame, myFrame;
     BOOL hDidChange = NO, vDidChange = NO;
     float gravity = 46;
     
     theWindow = [notification object];
     myFrame = [theWindow frame];
     e = [frames objectEnumerator];
     
     if (NSApp currentEvent] modifierFlags] & NSAlternateKeyMask) return;
     
     //NSLog(@"%d", [[NSApp windows] count]);
     
     while (edgeObject = [e nextObject])
     {
 	if ( (edgeObject != theWindow 
 	    && ([edgeObject respondsToSelector:@selector(isVisible)] && [edgeObject isVisible]))
 	    || [edgeObject isKindOfClass:[NSScreen class )
 	{
 	    // leave out the following if clause and just set frame to [edgeObject frame] 
 	    //if you want windows to snap behind the dock
 	    if ([edgeObject isKindOfClass:[NSScreen class]])
 	    {
 		frame = [edgeObject visibleFrame];
 	    }
 	    else
 	    {
 		frame = [edgeObject frame];
 	    }
 			/* code continues here with */ /* horizontal magnet */
 

----

For those who want to do window snapping (which is what *I* call it), I recommend the following source example:


*http://www.versiontracker.com/dyn/moreinfo/mac/17975



Basically it demonstrates how to move the windows around yourself, instead of trusting the window manager to do it for you. Normally of course you want to trust the window server, but you can't both trust the window server and get decent snapping on Jaguar.

-- MikeTrent

----

Very nice code!  I will definitely adopt it, however, I'll have to make my own modifications so it will snap to other windows instead of the screen edge.  Next up, gluable windows :)  Dragging one window, pulls the other along with it! 

-- JamesCallender

----

Ok, was easily able to adapt the lovely code for magnetic windows, but I have found that there is too much lacking to the functionality of these windows (such as no window bar, flaky attention and stuff... all a result of being ignored by the WindowManager I guess).  I have even gotten close enough to be able to send a message after every pixel drag, but unfortunately setFrame calls are not respected by the WindowManager.  It seems there is no way to do this without rewriting most of the code that handles window attention, and drawing window bars, and handling presses to the basic window buttons (close, minimize, etc.)

Any ideas?

-- JamesCallender

----
I've figured out how to get the window's actual location during dragging (rather than the location the drag started at), and I've even gotten [windowName frame] to return that real location. The thing I'm having trouble with is figuring out how to get the window to move during a drag, since setFrame doesn't work.

----

I've been thinking about implementing MagneticWindows for my app, since I've got a main window and a bunch of floating inspectors. I've realized that it's easy enough to control resizing the window in a live manner. The issue of      windowDidMove:  not being called except at lulls and the other issue of      setFrame  not working bother me.

What I've been wondering, but I don't know exactly how to implement since i'm not a grand-master cocoa programmer, is wether one could "fool" the window server into thinking a window drag has ended as soon as it begins. Then you could have a polling thread or timer move the window programmatically to follow the mouse. E.g., when you detect a     mouseDragged:  event on the NSWindow ( from NSResponder ) immediately send some sort of message to the  ( app? window? whom? ) that says "mouse up" or something. The window server might then stop dragging the window, and you could programmatically do the rest.

Is this reasonable? Thoughts?

--ShamylZakariya

----

Probably the easiest thing to do at this point would just be to use Carbon windows. Carbon has the API in place to track frame changes live.

----

I have not tried it, but considering that Cocoa supports obtaining a WindowRef from a pure Cocoa window, I suspect you could install a Carbon event handler at some convent point and handle it.

*Nope. I've tried it before. Cocoa windows don't generate Carbon events.*

----

***Edit 7 October 2004:** added to code above to prefer snapping windows to the screen edges over nearby window edges.*

