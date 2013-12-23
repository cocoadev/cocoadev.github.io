---
layout: page
title: NSDrawerAndLiveResize
---



In a drawer I have table view, whose row height I change in response to its NSViewFrameDidChangeNotification's. The only problem is that during live resizing the table view gets a bit messed up, since it preserves most of its content until the resize is finished. After changing the row height i call setNeedsDisplay:YES on the table view, but it still doesn't update properly until after the resize. I've set setPreservesContentDuringLiveResize:NO on both the window that owns the drawer, and on the table view's owning window, but it still tries to optimize for live resizing. Am i just doing really weird stuff, or is this a bug in the appkit? I could solve the problem by subclassing the table view, but I can't see why i should really need to do that.

/Mr. Fisk

----

If I properly understand what you're asking, subclass NSTableView and implement the following:

    - (BOOL) _wantsLiveResizeToUseCachedImage;
{
    return NO;
}

- (BOOL) _shouldLiveResizeUseCachedImage;
{
    return NO;
}

----

Thanks for the tip, but what I'm actually wondering is if the behavior I'm getting is the proper one, or if it's a bug in the appkit. Because, and i quote: 

**

- (void)setPreservesContentDuringLiveResize:(BOOL)flag

If flag is YES, the window optimizes live resize operations by invalidating only the view contents that changed; this is the default setting. If flag is NO, this optimization is disabled for the window and all of its contained views.

**

