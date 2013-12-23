---
layout: page
title: DetectingWhenWindowsChangeOrder
---



I know I can check if a window is key or not by using isMainWindow or isKeyWindow, but they way I understand it you'd need an NSTimer in order to get your action to fire when my app's windows change order. Are there any methods of NSWindow's that fire when it becomes inactive or changes order?

If anyone is curious about what I'm trying to do, I'm trying to make the windows in my app turn transparent via setAlphaValue after being inactive after a certain amount of time.

Thanks!

----

Use the window delegate methods:

    
- (void)windowDidResignKey:(NSNotification *)aNotification
- (void)windowDidBecomeKey:(NSNotification *)aNotification


and use NSApp's method:

    
- (NSArray *)windows


--zootbobbalu

