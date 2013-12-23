---
layout: page
title: NSTableViewFooterViewOverlay
---



Hi Cocoa People!

I'm trying to place a 'footer' view at the end of table views. Basically, I'm looking for a custom view to be placed within the scroll bar of a table view and stuck to the bottom. I've photoshopped a few images to help visualise the situation.

Original Table and Custom View

http://i.domaindlx.com/virux/tableBefore.jpg

Photoshopped Table and Custom View

http://i.domaindlx.com/virux/tableAfter.jpg

If you think of the spotlight search result window, or Safari's RSS viewer, the sidebar to the right of the window stays in position while the results are scrolled. Well think of that, but on the bottom of the table.

The footer must be a custom view, because it needs to handle drag and drop methods.

Any Ideas? I hope I haven't confused anyone with the many descriptions, but hopefully those images will work it out.

Cheers!
Louis

--

Subclass NSScrollView, add your own custom view to it, override - (void)tile to handle the tiling between the scrollers, rulers, the content view and your custom view. 

Good luck.
Bertrand.

--

Thanks! I did manage to get the correct behaviour. However looking at the design, I want to try having the table still continue under the footer, and the footer be slightly transparent. So naturally, you overlay the custom view over the table. BUT. When scrolling, the scroll view wants to draw the custom view elsewhere (up or down) accordingly, however the view still remains active in its original position!?!? How could I keep the custom view stationary while scrolling?

--

Anyone?

----
Your problem is due to the fact that NSClipView by default copies the contents of the screen around when scrolling. This is more efficient, since it's faster to just copy the pixels than force them to be redrawn. But here, you have another view overlapping things that shouldn't scroll, and so stuff breaks.

You have two possible solutions. One is to use     setCopiesOnScroll:NO, forcing the entire scroll view to redraw every time it's scrolled. This is inefficient, but an easy fix. The other solution is to put your semitransparent view into an overlay window. This is harder, but will preserve the copy-on-scroll behavior and be more efficient.

----
I'm not the original poster, but I'm doing something very similar in a project, so thanks! One question though, by an overlay window, you mean an entirely new NSWindow instance, right? So just trying to use an NSView without     setCopiesOnScroll:NO wouldn't work no matter where the transparent view was in the window's view hierarchy?

----
Yes, an overlay window is a completely separate NSWindow instance, generally made borderless and set as a child window of the parent window.

As long as your NSView remains in the same window as the scroll view and overlaps the scroll view's document view, you'll be forced to disable copy-on-scroll. The copy-on-scroll behavior works (as I understand it) by copying pixels around directly in the window's back buffer, which ignores the view hierarchy entirely. So no matter where you put it, as long as your NSView is drawing into the window's back buffer, its contents can get incorrectly copied. This is basically one manifestation of Cocoa not supporting overlapping views very well.
----
I tried to use [setCopiesOnScroll:NO] and none of the views would respond. I tried the table, I tried the scroll view, i tried every single view in any combination but none would respond to the command. So I tried using the overlay window method instead. Which worked out very well. I moved from 3 subclasses to only 1 which makes things much easier. Thanks Everyone! If you would like the source code to this result, drop me an email and I'll send you the Xcode Project.
----
I have created a class for this purpose. Drop into IB or allocate in your code. LKOverlayWindow

