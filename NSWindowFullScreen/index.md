---
layout: page
title: NSWindowFullScreen
---

*Editor's Note:* The original Q&A here about how to switch back and forth with full screen using OpenGL has been appended to the page OpenGLFullScreenToggle.

----

My goal: to open a full-screen borderless helper window in my app, at a window level
above even the Dock (known as StatusWindowLevel or ScreenSaverWindowLevel in NSWindow constants).

 This is a document-based app, and the document nib file contains a custom view description of the content view for the full-screen window,
and the window controller and window description for the main document window.
The helper window has a button connected to the document's window controller to close (or hide) it,
and put the document window back in front as key window.

The helper window is instantiated from an NSWindow subclass that implements

    
- ( BOOL ) canBecomeKeyWindow
{
	return YES;
}


The window controller for the document implements

    
- ( void ) awakeFromNib
{
...
	helperWindow = [ [ HelperWindow alloc } initWithContentRect: [ [ NSScreen mainScreen ] frame ]
		styleMask: NSBorderlessWindowMask backing: NSBackingStoreBuffered defer: YES ];
	// Restore the original view from the nib file with all the controls
...
}


----

The line     helperWindow = [ helperWindow initWithContentRect: [ [ NSScreen mainScreen ] frame ] styleMask: NSBorderlessWindowMask backing: NSBackingStoreBuffered defer: YES ]; is incredibly evil, and I'm surprised it doesn't crash. You are *not* allowed to re-initialize an object like that. To create a borderless window, make an NSWindow subclass that overrides the init method and calls super with the appropriate mask. You should create a separate window controller and have it manage the window. If you fix those problems, it should work more or less properly.

*OK. I'm not re-initializing the window now, but rather, (see just below) creating a single instance in the window controller for the document. Is that better?*

----

I do this in the     awakeFromNib method in the document's window controller

    
	// Put up a new window
	helperWindow = [ [ HelperWindow alloc ] initWithContentRect: screenRect
		styleMask: NSBorderlessWindowMask backing: NSBackingStoreBuffered
		defer: NO screen: [ NSScreen mainScreen ] ];


If I don't create an object of the HelperWindow class, the helper window cannot become key.
If I try to initialize the helper window in the init method of the HelperWIndow class, it doesn't get the content view from the nib file.
Bad news, indeed.
I now release the window, also in the document's window controller:

    
- ( void ) dealloc
{
...
	[ helperWindow release ];
...
}


Each time I create a new document, of course, I get a new helperWindow id. I can live with that, I guess.

I can log from the     dealloc method of the NSWindow subclass that implements     canBecomeKeyWindow and sure enough
the id of the last window allocated is listed when the doc window is closed. It's the same id as the one that was allocated in the window controller.
But the object is not really being deallocated.

Then when I quit the app, I see those objects released - AGAIN. ObjectAlloc shows a count of the helper window class objects
increasing once each time the app creates a new document. And the count doesn't go down when the doc windows are closed -
so it is a bona fide memory leak.

Can somebody explain to me what memory management
gaffe this is causing? In other words, why won't the runtime REALLY release the helper windows when the document window is closed
(and the dealloc method in the window controller executes)???

----

*A possible solution, in case you're still interested, might be to create a window controller specifically for your "helper" window, and create and destroy the full-screen window entirely within the auxiliary window controller. The approach you have outlined above is weak, and you should not be surprised that it causes memory leaks.*

You might want to check out ImplementingFullScreen and BorderlessWindow. The discussion in SigTrapDuringFullScreen may help, though the problem discussed there is not actually solved. HTH.

