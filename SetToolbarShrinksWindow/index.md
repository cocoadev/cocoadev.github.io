---
layout: page
title: SetToolbarShrinksWindow
---



I'm having a really bizarre problem with NSWindow's     setToolbar: method. I have two windows which I want to appear as being the same window, but with different behavior. Only one of them is onscreen at any given time. I want them to share the toolbar. Since NSToolbar apparently doesn't appreciate being set in two windows at once (I get weird drawing behavior that way), I swap out the toolbar like this when I switch:
    
[window1 setToolbar:nil];
[window2 setToolbar:toolbar];

And vice versa when switching back the other way. Now, here is the weird part: when I switch from window2 back to window1, the     setToolbar: call causes window1 to shrink so that it exactly fits the toolbar, completely obliterating the contents of the window. I can reset it, but this resizing dance screws up the location of the contents, since this is (obviously) way smaller than my minimum size.

What could cause this, and more importantly, how can I fix it? The toolbar is retained, so it's not a memory management problem. It only occurs to one window, and then only when I do this swap. (My code also does a     [window1 setToolbar:toolbar] on startup and there is no problem.) I'm clueless as to how I can fix this. Any ideas? -- MikeAsh

----

A followup, since I've worked around this problem in a fairly easy way. It turns out that toolbars aren't very complicated. (I inherited this code and didn't look at the stuff that set up the toolbar very hard.) I create two instead of just one, and they both share the backend items. With a bit of extra code to keep them synchronized, I'm in business. One gotcha I ran into is that not only do NSToolbars not like being in more than one window, but NSToolbarItems don't like to be in more than one toolbar. I had weird drawing glitches until I modified my delegate method to return a copy of the item in my dictionary.

I still have no idea about the cause of the original bug. If anybody comes up with something, I'd love to hear it, but fortunately it's no longer a problem for me. -- MikeAsh

----

Not sure if the following may be relevant, but here goes...

Apple gives an example using the following code (you may be able to find it with a text search of the documentation, or googling)

    
- ( float ) toolbarHeightForWindow: ( NSWindow * ) window
{
	NSToolbar *toolbar;
	float toolbarHeight = 0.0;
	NSRect windowFrame;
	toolbar = [ window toolbar ];
	if ( toolbar && [ toolbar isVisible ] )
	{
		windowFrame = [ NSWindow contentRectForFrameRect: [ window frame ] styleMask: [ window styleMask ] ];
		toolbarHeight = NSHeight( windowFrame ) - NSHeight( [ [ window contentView ] frame ] );
	}
	return toolbarHeight;
}

// and, for a crude example, when archiving the window's frame data

	float toolbarHeight = [ winCtl toolbarHeightForWindow: [ winCtl window ] ];
	NSRect saveRect = NSMakeRect( frameRect.origin.x, frameRect.origin.y + toolbarHeight,
		frameRect.size.width, frameRect.size.height - toolbarHeight );



Obviously, restoring the toolbar has to take place *after* this window frame is restored from a file, or whatever else analogous is going on

