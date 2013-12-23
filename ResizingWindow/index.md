---
layout: page
title: ResizingWindow
---

To resize a window, you must send it a     -setFrame: passing a valid NSRect using the screen's coordinate system. This is most often accomplished by getting the window's frame first, then modifying it.

    
- (void)resizeWindowToMyFavoriteSize
{
    // Get and modify the window's frame
    NSRect frame = [myWindow frame];
    frame.size.width = 200;
    frame.size.height = 200;

    // Now set the window's frame
    [myWindow setFrame:frame];
}


You can animate your resizing behavior by using the NSWindow method      - (void) setFrame: (NSRect) frame display: (BOOL) dflag animate: (BOOL) aflag

For issues related to positioning of subviews when a window is resizing, see MovingItemsWhenResizingWindow

For advice about how to do resizing with multiple threads, see MultiThreadedWindowResizing

There is also a WindowPosition page

----

**Discussion**

I'm resizing my NSWindow like this     setFrame:frame display:YES animate:YES  except that my window exapnds upwards -- the bottom edge stays where it is, the top edge expands upwards.  Is there an easy way to get the opposite effect -- have it expand downwards, without writing my own loop that animates the thing itself -- that would be really wrong if I had to do that.

----

The coordinate system in Cocoa is measured from the bottom left rather than the top left, so you need to move the frame rect's origin down by the same amount as you're increasing the height to get your window to expand downward.  -- Bo

