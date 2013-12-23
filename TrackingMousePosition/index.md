---
layout: page
title: TrackingMousePosition
---

Tracking Mouse Position snippet:

    
TRACKING MOUSE POSITION

	//declare rect
	General/NSRect myRect = General/NSMakeRect(10,10,30,90);
	General/NSPoint loc = General/self window] mouseLocationOutsideOfEventStream];
	[[NSLog(@"%s",General/[NSStringFromPoint(loc) cString]);
	//	General/NSLog(@"rect is: %s",General/[NSStringFromRect(parentFrame) cString]);

	if ([self mouse:loc inRect:myRect]){
		General/NSLog(@"got it!");
		[testWindow makeKeyAndOrderFront:nil];
		[testWindow setAlphaValue:.5];
		[self animateVortex];
	}



 

General/EcumeDesJours

keywords: tracking, hotspot, hotzone, mouseposition

----

Also     General/[NSEvent mouseLocation]

*
+ (General/NSPoint)mouseLocation

Exports the current mouse position, in screen coordinates. Similar to General/NSWindow�s mouseLocationOutsideOfEventStream, this method returns the location regardless of the current event or pending events. The difference between these methods is that mouseLocationOutsideOfEventStream returns a point in the receiving window�s coordinates and mouseLocation returns the same information in screen coordinates.
*
