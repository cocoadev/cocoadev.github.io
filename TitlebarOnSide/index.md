---
layout: page
title: TitlebarOnSide
---

How do I create a General/NSPanel which displays with its titlebar on the left hand side?  I know that I've seen this style of window before on multiple occasions and I'm pretty sure some of those have been in cocoa apps.  It's usually used for floating toolbars (I think it's used this way in Word, among other applications) and that's exactly what I need to use it for.
--Reid Beels

----
I'm *pretty* sure you'll have to create your own subclass, set the window's style mask to General/NSBorderlessWindowMask, then draw your own directly to the window's contentView, tracking dragging through the rect of your custom  titlebar mockup.

----
I've seen it in Carbon apps, never in Cocoa. Word isn't Cocoa. You could probably create a Carbon window like that from Cocoa.

----
I haven't tested this, but it looks like you have to create the window using carbon then use General/NSWindow's initWithWindowRef:

----
I got this working, partially. It doesn't draw the window correctly, but it shows up. Any ideas on how to make it work better (it definitely is a Carbon window still)? Also, it seems that using setFrame:display: doesn't work correctly with the coordinates. In the code below I manually create the bounds. --General/KevinWojniak

    
General/NSView *contentView = // some view
General/NSRect frame = General/NSMakeRect(100, 100, General/NSWidth([contentView frame]), General/NSHeight([contentView frame]));
General/NSWindow *cocoaWin = nil;

General/WindowRef carbonWin;
Rect bounds = {General/NSMinY(frame) + General/[[NSStatusBar systemStatusBar] thickness],
			General/NSMinX(frame) + 12,
			General/NSMinY(frame)+General/NSHeight(frame),
			General/NSMinX(frame) + 12 + General/NSWidth(frame)};
General/OSStatus res = General/CreateNewWindow(kFloatingWindowClass,
							   kWindowCloseBoxAttribute |
							   kWindowCollapseBoxAttribute |
							   kWindowStandardHandlerAttribute |
							   kWindowCompositingAttribute |
							   kWindowSideTitlebarAttribute |
							   kWindowResizableAttribute,
							   &bounds,
							   &carbonWin);

if (res == noErr)
{
	cocoaWin = General/[[NSWindow alloc] initWithWindowRef:(void *)carbonWin];
	[cocoaWin setContentView:contentView];
	[cocoaWin makeKeyAndOrderFront:nil];
	// from here on out it gets funky...
}


----
I got it working fine by General/ORing the undocumented value ( 1 << 9 ) to the style mask for an General/NSPanel. The close button is going to look way too big, but if you add General/NSUtilityWindowMask, it looks and works fine (at least on Leopard). I guess we should all lobby Apple to document that value so they don't break it in future versions.
