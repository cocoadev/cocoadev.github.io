---
layout: page
title: NSOpenGLViewNSSplitViewProblem
---



I have an NSOpenGLView that reshapes fine when it is the content view of a window, but when I place this view in an NSSplitView the NSOpenGLView does not autoresize very well. When the divider of the split view is changing the size of the NSOpenGLView the visible space for the glView does not get flushed properly. Artifacts are left from the trailing edge of the divider and even the content of a neighboring view that is being decreased in size will be left as artifacts in the space reserved for the glView. Anyone else have this problem?

----

I've never had the problem; but then, I've never put an NSOpenGLView into a split-pane. If I were you, I'd write a throwaway app with a simple NSOpenGLView that just draws a rectangle or something. You can put in NSLog traces to resize events and calls to draw and so on an figure out what's getting foobarred.

--ShamylZakariya

----

I'm guessing that NSOpenGLView is using a child window to do its drawing. A child window that gets cropped by a subview placed in a splitview acts the same way. --zootbobbalu

----

Here's a solution to this problem I ran across today. 

http://developer.apple.com/samplecode/GLChildWindowDemo/GLChildWindowDemo.html

The basic idea is to use a Carbon hack to get the view to sync with the live resizing coordinated by a split view. You have to subclass the view's window and add these two methods.     needsEnableUpdate is an instance variable of type     BOOL. 

    

- (void)disableUpdatesUntilFlush {
	if (!needsEnableUpdate) DisableScreenUpdates();
	needsEnableUpdate = YES;
}

- (void)flushWindow {
	[super flushWindow];
	if (needsEnableUpdate) {
		needsEnableUpdate = NO;
		EnableScreenUpdates();
	}
}



Then you have to modify the NSOpenGLView subclass to disable window updates temporarily while the split view is resizing.

    

- (void)viewWillMoveToWindow:(NSWindow *)win {
	if ([win respondsToSelector:@selector(disableUpdatesUntilFlush)]) {
		[[NSNotificationCenter defaultCenter] addObserver:self
			selector:@selector(splitViewWillResizeSubviewsNotification:)
			name:NSSplitViewWillResizeSubviewsNotification
			object:enclosingSplitView];
	}
}

- (void)splitViewWillResizeSubviewsNotification:(NSNotification *)note {
	self window] performSelector:@selector(disableUpdatesUntilFlush)];
}

- (void)dealloc {
	[[[[NSNotificationCenter defaultCenter] removeObserver:self];
	[super dealloc];
}



--zootbobbalu

