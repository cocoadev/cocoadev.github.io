---
layout: page
title: SubclassingNSWindow
---

How do you sub-class General/NSWindow in order to handle key-presses that aren't handled elsewhere?

----

(March 25, 2002)

Why do you want to? Will changing its main view not do the trick? A reason might help someone find a good example code, such as the one to change the shape of the window... -- General/KritTer, with apologies for the lack of an actual answer

----

(March 25, 2002)

General/NSWindow is one of those weird objects that require you to subclass them to change aspects of their behavior. For example, you need to subclass General/NSWindow in order to override canBecomeKeyWindow and canBecomeMainWindow. You can't do everything through a delegate. Still, this isn't a problem for normal apps.

To answer the question more completely: you subclass General/NSWindow just like any other object. Does this help?

-- General/MikeTrent

----

(March 26, 2002)

The solution was to simply subclass it, import the class in IB, and change the class of my window in IB to my new class. I had to override the designated window initializer:

(id)initWithContentRect:(General/NSRect)contentRect styleMask:(unsigned int)styleMask backing:(General/NSBackingStoreType)backingType defer:(BOOL)flag

and I declared a category for General/NSResponder containing new first responder messages. Then I dispatched messages to my first responder using the General/NSWindow method tryToPerform:.

----

Yup, as I said, just like any other object -- General/MikeTrent

----

There are a number of bugs relating to frame autosaving.  Frame autosaving is supposed to save a window's position and size automatically, every time a window is moved or resized. When the window is shown again, it's supposed to restore these values.  Unfortunately, as of Mac OS X 10.1.5, there are a number of bugs in this mechanism.  For some guesses as to the problems and workarounds, see General/NSWindowFrameAutosizing. --General/NicholasRiley

----

My application has three windows open at the same time, let's call them A, B and C.

Window A is in front.

I want to be able to click on B to bring it to the front, but clicking on C should simply beep as if A is modal.

The closest I can get to this behavior is to send -setIgnoresMouseEvents to window C but that simply makes the window transparent and will act as if I clicked on whatever was behind window C.

How can I intercept that mouseDown message before it causes the window to move to the front?

----

Sounds like a UI disaster to me. Couldn't you combine A & B into one window? Using a tab view or drawer or something?

Otherwise, try making window C a General/NSWindow subclass, and override     becomeKeyWindow and     becomeMainWindow to do nothing.

----

Overriding     becomeKeyWindow and     becomeMainWindow to do nothing still does not prevent the window from layering itself over the other windows.

I thought this might have something to do with the Window Server because it seems that I can't override the mouseDown event in a subclass of General/NSApplication to stop it it before it causes the window to layer itself to the top.

----

Try using General/NSPanel for window C to prevent this window going to front.

----

To prevent a window from being ordered to the front when it is clicked, you must subclass the General/NSView inside the window and override the following methods:

    
- (BOOL) shouldDelayWindowOrderingForEvent: (General/NSEvent *) theEvent
{
	return YES;
}

- (void) mouseDown: (General/NSEvent *) theEvent
{
	// [...]
	General/[NSApp preventWindowOrdering];
	// [...]
}


-- General/ChrisAshworth

----
Wouldn't the proper thing to do here be to make A actually *be* modal, then do a     setWorksWhenModal: on B so that it keeps functioning?

----
Not if your goal is to keep your window behind those of other applications as well as your own.

----
The point is that this shouldn't be your goal. Cocoa already has a nice API for doing modal windows which works well and provides a consistent user experience across all applications. If you use this nice API instead of trying to do strange things to fake it badly, you'll get this consistent user experience in your app, and have easier coding too.

----

Sometimes you really do need to prevent a window from coming forward when it is clicked.  In my case, my app displays layers of video during live performances.  Each video is displayed in a window, and the windows may or may not overlap each other.  The video is triggered by an operator.  It's important that the layering of the windows not be adjusted if the operator accidentally clicks on one of those windows.  So yes, there are legitimate designs where this is exactly what you need to do, and the above code is how to do it.

-- General/ChrisAshworth
