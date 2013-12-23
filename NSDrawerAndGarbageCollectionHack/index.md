---
layout: page
title: NSDrawerAndGarbageCollectionHack
---

http://www.cocoabuilder.com/archive/message/cocoa/2008/10/27/221105 describes a current bug in NSDrawer that causes it to become detached from it's parentWindow when it resizes if garbage collection is on. The hack below forces the drawer to stay attached.

    
@implementation NSDrawer (GC)

- (void) gc_fix:(NSNotification *)notification
{
	[self setContentSize:[self contentSize]];
}

- (void) awakeFromNib
{
	[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(gc_fix:) name:NSWindowDidResizeNotification 
		object:[self parentWindow]];
}

@end


It should be understood that the intent is that this is a temporary work-around until the bug is fixed in AppKit. — BryanWoods

Update: This code is no longer needed.

