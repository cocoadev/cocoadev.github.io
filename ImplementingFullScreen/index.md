---
layout: page
title: ImplementingFullScreen
---



Is there any simple way of making a window full-screen?  Full-screen meaning the Title-bar of the window is gone, as is the menu bar, and all you can see is the window's contentView.  If there isn't, what's the best way of doing this? 

----

I'd like to extend this with a second question; is there any way of making a window full-screen such that the menu bar is still usable but the Dock is not? (I believe Photoshop does this with one of its windowing modes.)

----

For the first question, take a look at DisplayKit [http://www.zobs.net/brian/software/].  It provides basic screen capturing, resolution switching and gamma fading functions.


For the second question, I'm not 100% sure how to go about this but I'd guess you'd have to manually create a window with a style mask of NSBorderlessWindowMask and a content rect of the screen's frame (minus the menu bar height) and then call [window setLevel:NSFloatingWindowLevel];  and then do whatever drawing you want in that window.  -- Bo

----

I managed to get this to work with a single line of code. And with Cocoa, you can animate the window if you want. Useless, but very cool for the eye.
    
[myWindow setFrame:theScreenFrame display:YES animate:YES];


Assuming myWindow is your NSWindow and theScreenFrame is a NSRect representing the frame of the screen. If you want to get the frame of the screen, you can do something like this :
    
NSRect theScreenFrame = [[NSScreen mainScreen] frame];


That's it. I suggest that before you make any change to your window, you keep the old window size in another NSRect if you want to return from your full screen mode.

-- Trax

----

There's a tutorial on this subject here: http://cocoadevcentral.com/articles/000028.php
( I haven't really read through it, so if it's useless feel free to delete this )

DanMessing

----

Also, there's a technote from Apple about creating kiosk type applications for Mac OS X:

http://developer.apple.com/technotes/tn2002/tn2062.html

-- AndreasMayer

----

I would try using the QuickTime function BeginFullScreen and the NSWindow init-with-window-ref stuff.

----

The tutorial at cocoadevcentral is OK as far as it goes.

My goal was to create a borderless, full screen, not-for-graphics-display window launched in a modal session but with
some controls present, (including, of course, one to put the window away and end the modal session.)
This window was initially formulated in the nib file, along with its set of widgets and connections.

When I re-initialized my auxiliary window outlet with code to open a full screen borderless window, it totally
screwed up the functionality of some controls drawn within the content view, although strangely enough,
the critical button to close the window and end the modal session worked well enough to bail out of the problem
without killing the process back in PB.
The cocoadevcentral article points out that borderless windows cannot become key 
(and the Cocoa documentation remarks that the canBecomeKey method for borderless windows 
returns NO by default, and suggests HIG-like that such things should be used for display only)

Procedure Used:

1) I subclassed my window and overrode the canBecomeKey method; changed the Custom Class of borderlessWindow in the nib file.
    
@implementation FullScreenWindow
- ( BOOL ) canBecomeKeyWindow
{
	return YES;
}
@end

2) In the awakeFromNib method for the window controller object, save the original content view for the to-be-borderless window
    
	NSView *oldView = [ borderlessWindow contentView ];

3) Reinitialize the window the way I want it
    
	borderlessWindow = [ borderlessWindow initWithContentRect: [ [ NSScreen mainScreen ] frame ]
		styleMask: NSBorderlessWindowMask backing: NSBackingStoreBuffered defer: YES ];

4) Restore the content view - all the connections and widgets work normally
    
	[ borderlessWindow setContentView: oldView ];


I imagine this whole procedure is even easier if you don't have to worry about any interface in the window besides a custom view,
or if you are not worrying about making the window borderless.

----

I don't know how to enter full screen and switch resolution, can someone give me the sourcecode for that?

*Apple has demonstrations of this in their sample code section of http://developer.apple.com/ - do a bit of googling if it's not immediately visible, because this is a **very** common thing to do.*
----

----

OmniGroup has some sample code that may be of interest to you: http://www.omnigroup.com/developer/gamedevelopment/gdc2001/

----

those examples dont run in xcode :-(

EcumeDesJours

----

How to make a window full screen but for the dock and menu:     [window setFrame:[[window screen] visibleFrame] display:YES].

