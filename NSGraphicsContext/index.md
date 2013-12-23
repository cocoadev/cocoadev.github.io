---
layout: page
title: NSGraphicsContext
---

[http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSGraphicsContext_Class/index.html]

The NSGraphicsContext class is the programmatic interface to objects that represent graphics contexts.




To avoid the following error in the console:

unlockFocus called too many time 

Make sure you're saving/restoring the graphics context properly.  Example:

	[NSGraphicsContext saveGraphicsState];
	
	[NSGraphicsContext setCurrentContext:[NSGraphicsContext graphicsContextWithGraphicsPort:context flipped:NO]];

	// draw something

	[NSGraphicsContext restoreGraphicsState];

