---
layout: page
title: OverridingLimitsOnWindowSizes
---

Right now, I'm trying to create a really narrow utility panel that is available system-wide.  I've got most of the behavior working right, but I'm having trouble getting the panel as thin as I want.  I'd like to set     minSize of my panel with a width of 27px, but something keeps bumping it up to 59px.

I've tried setting the minimum width from Interface Builder and from a few places in my code, but I've been unsuccessful.

So, the code.  In     MyController.h, I set the initial size of my window to 27x54:
    
//	MyController.m

#import "MyController.h"

@implementation MyController

- (void)awakeFromNib
{
//  Setting the initial size of the panel...
	NSRect frame = [myPanel frame];
	frame.size.width = 27;
	frame.size.height = 54;
	[myPanel setFrame:frame display:YES];

//  Behavior stuff...
	[myPanel orderFrontRegardless];
	[myPanel setHidesOnDeactivate:NO];
	myPanel standardWindowButton:[[NSWindowZoomButton] setHidden:YES];
	myPanel standardWindowButton:[[NSWindowCloseButton] setHidden:YES];
	myPanel standardWindowButton:[[NSWindowMiniaturizeButton] setHidden:YES];
}

@end


This code set the initial size correctly, but resizing the panel with my mouse set the width back up to 59px.  To fix this, I tried this code in     MyPanel.m (I subclassed     NSPanel and chose my subclass in the Custom Class menu in IB):
    
//	MyPanel.m

#import "MyPanel.h"

@implementation MyPanel

- (NSSize)minSize
{
	NSSize minSize;
	minSize.width = 27;
	minSize.height = 50;
	return minSize;
}
@end



Nothing changed.  Then, I took out the code above and tried the following in     MyController.m instead:
    
//	MyController.m

#import "MyController.h"

@implementation MyController

- (void)awakeFromNib
{
//  NEW CODE: Trying to set minSize -- doesn't work
	NSSize minSize;
	minSize.width = 27;
	minSize.height = 50;
	[myPanel setMinSize:minSize];

//  Setting the initial size of the panel...
	NSRect frame = [myPanel frame];
	frame.size.width = 27;
	frame.size.height = 54;
	[myPanel setFrame:frame display:YES];

//  Behavior stuff...
	[myPanel orderFrontRegardless];
	[myPanel setHidesOnDeactivate:NO];
	myPanel standardWindowButton:[[NSWindowZoomButton] setHidden:YES];
	myPanel standardWindowButton:[[NSWindowCloseButton] setHidden:YES];
	myPanel standardWindowButton:[[NSWindowMiniaturizeButton] setHidden:YES];
}

@end



That also didn't work.

The minimum height set successfully, so my code is at least partly working, but I can't set the min width below 59px.  Is it possible to override this?

To use the Japanese idiom, I ask you for your kind favor.

----

Wolf Rentzsch was twitting about something similar. He was trying to make a window taller than the screen. His solution that may work for you was to add a category -[NSScreen frame] that hard-codes NSMakeRect(0,0,10000,10000) allow him to setFrame to anything. 

----
I'm sorry for not being clear.  My code works for setting the size of the panel when my program starts up.  It's just that when I drag the resize box at the bottom right with the mouse, the GUI doesn't allow me to go smaller than 59x50 pixels (I want the minimum size to be 27x50).  Procedure is:

1. Build and run.  Window is set to correct size.

2. Try to resize window with mouse.  GUI pops the window size back up to a minimum of 59x50 pixels. (Minimum window size should be 27x50.)

----

AppKit imposes a minimum size on windows.  Try making it borderless and see if that helps.  --K

----
I could do that, but then there wouldn't be an obvious place where users can drag to move the window.  My panel is basically a vertically-oriented button bar.  If I do make my window borderless, is there a simple way that I could simulate a title bar?

Also, last I read, resizing borderless windows has to be done manually (http://www.cocoabuilder.com/archive/message/cocoa/2004/2/18/97332).  Is this still the case?

Thanks for your help!

----

Perhaps you should reconsider what you're doing.  AppKit maintains its minimum sizes so that the standard border widgets are accessible.  If you're going to be introducing a non-standard interface with this thin button bar, you might want to resign yourself to implementing a non-standard window frame.  Or, if you're targeting Leopard-only, depending on what you're doing, you might consider making an NSStatusItem with views inside its menu.  --K

----

OK, for lack of a better solution, I've decided to program the window's resizing and dragging from scratch.  For intuitiveness, though, I'd like to give it a standard utility panel title bar.  Could someone tell me where the resources for the title bar are, or will I have to resort to building the graphics for the title bar by hand from screenshots?  

I know, I'm lazy, but I'm also a bit of a purist; it'd be nice to have the originals.  Also, if my program is run on later versions of Mac OS X, the system graphics for the title bar might change.  This would make the appearance of my program inconsistent with the rest of Mac OS X.  Therefore, I'm thinking that links to graphics in the system resources would be better than hard-wired graphics stored in my program's resources.

----

Linking to resources in the private frameworks of the system (which is basically all graphical resources) isn't a good idea either, as they might also change in an update. There is no guarantee they will be there in 10.5.3, let alone a more important update. I think AppKit imposes a minimum width on standard frame windows so that the window controls (close, minimize, zoom) have space to exist. Removing some of these from the frame (not just disabling them) may help to allow your frame to shrink further. --LoganCollins

----

Hmm.  I didn't consider that the path to the resources could change.  But aren't there at least some symbolic links to the resources that I can use?  If programs from earlier versions of OS X can still run on the latest version using GUI elements from the new version of OS X, I think there must be some symbolic links.  Can anyone tell me how to use these?

Next, about removing buttons in the toolbar. I tried replacing these lines in MyController.m:

    
...
myPanel standardWindowButton:[[NSWindowZoomButton] setHidden:YES];
myPanel standardWindowButton:[[NSWindowCloseButton] setHidden:YES];
myPanel standardWindowButton:[[NSWindowMiniaturizeButton] setHidden:YES];
...


with these:
    
...
myPanel standardWindowButton:[[NSWindowZoomButton] removeFromSuperview];
myPanel standardWindowButton:[[NSWindowCloseButton] removeFromSuperview];
myPanel standardWindowButton:[[NSWindowMiniaturizeButton] removeFromSuperview];
...


I don't think I used the right method in either case.  

    setHidden worked in hiding the controls, but made no change in the window resizing behavior.  It was as though the buttons were still there, but invisible (which was probably the case). 

     removeFromSuperview froze my program, bringing up the debugger.  I used     NSLog() to see how far my program got before freezing, and it appears that it froze someplace *after* the three lines of code that I changed.  In the window's frozen state, however, the Zoom, Close and Min buttons did disappear like I wanted.

So, two questions now:

* What command(s) should I use to get rid of the buttons and/or allow me enough freedom in resizing my window?
* How can I link against the system GUI resources?


I'd really like to get the first question answered, but if I can't, I could live with an answer to the second question.  Thanks!

----
Retain the buttons before you remove them. They're being deallocated and this is leading to a crash.

As to the question of how older apps can use new GUI resources, the answer is simple. The older apps load the latest frameworks, and those *frameworks* are what load the resources. They know where they are because they are constantly updated, but that doesn't mean *you* can know where they will be.

*To add more to what the previous poster said, you have to keep the buttons around because the window's still going to send messages to them, even if they're not actually in the title bar. Release them later when you're sure the window is gone. Removing the buttons is unsupported anyway...it might just be easier to do a custom resize control rather than this. --JediKnil*

----
Thanks, JediKnil.  The retain tip worked in preventing my program from crashing:

    
...
[myPanel standardWindowButton:[[NSWindowZoomButton] retain] removeFromSuperview];
[myPanel standardWindowButton:[[NSWindowCloseButton] retain] removeFromSuperview];
[myPanel standardWindowButton:[[NSWindowMiniaturizeButton] retain] removeFromSuperview];
...


But it was still impossible to resize the window as small as I wanted.  For now, I have resigned myself to manually programming the window behavior.

Now, what code exactly do I need to link against the system resources and manually draw the utility-panel style title bar?

----

You can download and have a look at the source to the OmniInspector framework for more information on how to do this: http://www.omnigroup.com/ftp/pub/software/Source/MacOSX/Frameworks/

----

Have you seen -[NSWindow (NSRect)constrainFrameRect:(NSRect)frameRect toScreen:(NSScreen *)screen]? Try overriding that in a subclass to return frameRect.

----

I tried to compile the OmniInspector source, but I couldn't because I'm using Mac OS 10.4.11; OmniInspector requires Leopard.  Inside the project, I did find some graphics for titlebars and things, but it would still be nicer to link against the system resources.  If I can't do that, I will probably use these graphics (I assume that The Omni Group's source code license also covers source graphics: http://www.omnigroup.com/developer/sourcelicense/).

I also tried to subclass NSPanel and overriding     constrainFrameRect: toScreen:.  Using     NSLog(), it appeared that the method was only called when the window is told to reveal itself and when I move the window by its titlebar.  The method was NOT called when I resized the window, and telling the method to return     frameRect had no effect.  Here is the code:

    
//MyPanel.m

#import "MyPanel.h"

@implementation MyPanel

-(NSRect)constrainFrameRect:(NSRect)frameRect toScreen:(NSScreen *)screen
{
	NSLog(@"constrainFrameRect:{ { %f, %f } { %f, %f } } toScreen:%@",
		  frameRect.size.width,frameRect.size.height,
		  frameRect.origin.x,frameRect.origin.y,
		  screen);
	return frameRect;
}

@end



Here is the run log, with my comments after double slashes:
    
[Session started at 2008-03-24 15:25:15 -0500.]
2008-03-24 15:25:15.422 My App[4798] constrainFrameRect:
	{ {360.000000, 256.000000} {396.000000, 264.000000} } toScreen:(null)
2008-03-24 15:25:15.441 My App[4798] Window buttons hidden
2008-03-24 15:25:15.443 My App[4798] constrainFrameRect:
	{ {30.000000, 104.000000} {396.000000, 264.000000} } toScreen:(null)
2008-03-24 15:25:15.537 My App[4798] constrainFrameRect:
	{ {30.000000, 104.000000} {396.000000, 264.000000} } toScreen:(null) 
	// My code sets the window size
2008-03-24 15:25:36.593 My App[4798] constrainFrameRect:
	{ {30.000000, 104.000000} {548.000000, 223.000000} } toScreen:(null) 
	// I move the window w/o changing the window size.
2008-03-24 15:25:45.169 My App[4798] constrainFrameRect:
	{ {136.000000, 135.000000} {502.000000, 170.000000} } toScreen:(null) 
	// This came only after I had resized, then moved the window.  
	// (No new log entry was added when I resized the window.)

My App has exited with status 0.


So, in summary, I'm still looking for a way to make a utility window narrower than allowed by Cocoa.  Any suggestions?  Will I have to manually program the window behavior?  If so, how can I link against the system resources for the graphical elements of the window?

Here is what I've tried:

*
Setting minSize in Interface Builder - didn't allow me to set minSize with a width less than 59px
*
Manually set minSize in code - Didn't work. Something bumped the window back up to 59px in width when I used the resize box (I had set a minimum width of 27px).
*
Removing title - no effect
*
Hiding buttons in titlebar - also no effect
*
Removing the titlebar buttons from the superview - no change
*
Overriding     constrainFrameRect:toScreen: to return the inputted frameRect with no modification. - Didn't work, because the method is only called before the window appears on screen and when the window is moved.  Overriding the method did NOT alter the resizing behavior.


Again, I ask for your kind favor.  Thank you!
----

There's an entire theme manager under the hood that's responsible for drawing the windows in certain ways.  It would be a massive undertaking to reverse-engineer it.  There's no simple solution to what you want to do.

