---
layout: page
title: BorderlessWindow
---



*Additional discussion in WindowsWithoutDecoration and SetMovableByWindowBackground*

You can create windows that don't contain a title bar or other adornment by overriding     [NSWindow initWithContentRect:styleMask:backing:defer:] as follows:

<source lang="objc">
- (id) initWithContentRect: (NSRect) contentRect
                 styleMask: (unsigned int) aStyle
                   backing: (NSBackingStoreType) bufferingType
                     defer: (BOOL) flag
{
    if (self = [super initWithContentRect: contentRect
                                styleMask: NSBorderlessWindowMask
                                  backing: bufferingType
                                    defer: flag])

    {
       // other initialization
    }
    
    return self;
}
</source>

If you are creating the window programmatically (i.e., not through InterfaceBuilder), just pass 

    NSBorderlessWindowMask in the     styleMask argument to the normal     NSWindow init function. Note that     NSBorderlessWindowMask has a value of zero, so it cannot be combined with other style mask values.

Note that TrackingRectangles and CursorRects do not work in borderless windows unless you also override     -canBecomeKeyWindow as follows:

<source lang="objc">
- (BOOL) canBecomeKeyWindow
{
   return YES;
}
</source>

There is some sample code for a borderless window at:

http://developer.apple.com/samplecode/RoundTransparentWindow/index.html

Use an NSBox as your or other custom view that you lay out yourself (don't have the background image, or use a fake background image of your choosing) you can create a fake window. Then set the window's content view to it. Drag and drop might be a problem, but you can work that part out.

----

It seems that if I use NSBorderlessWindowMask to make a window, then my window behaves strangely with respect to becoming and resigning as key window.   

I know that it's documented that if you use this window mask, then your window will never become key by default, so Apple's sample code (RoundTransparentWindow) recommends overriding -canBecomeKeyWindow to always return YES.  This allows the window to become key, and it does receive windowDidBecomeKey messages, but when I click the window to make it key, other normal windows don't change their appearance to the non-key look.  Also, my custom window does not automatically darken its shadow like normal windows do when they become key.  

If I remove that window flag, then everything works as expected.

Is anyone else seeing this?  Does anyone know if there's a way around these issues?

-KenAspeslagh

----

I can verify this behavior. The only workaround I can think of is to draw your own drop shadow. Or, alternatively, file a bug in radar.

-RobertChin

----

**A Simple Example**

DXFloatingWindow.m (Subclass of NSWindow)

<source lang="objc">
#import "DXFloatingWindow.h"

@implementation DXFloatingWindow

- (id)initWithContentRect:(NSRect)contentRect styleMask:(unsigned int)aStyle backing:(NSBackingStoreType)bufferingType defer:(BOOL)flag
{
	self = [super initWithContentRect:contentRect styleMask:NSBorderlessWindowMask backing:NSBackingStoreBuffered defer:YES];
	[self setMovableByWindowBackground:YES];
	[self setBackgroundColor:[NSColor clearColor]];
	[self setLevel:NSNormalWindowLevel];
	[self setOpaque:NO];
	[self setHasShadow:YES];
	
	return self;
}

</source>

DXQuadroJanela (Subclass of NSView)

<source lang="objc">
#import "DXQuadroJanela.h"

NSImage *fundoDX;

@implementation DXQuadroJanela

- (void)awakeFromNib
{	
	fundoDX = [[NSImage alloc] initWithContentsOfFile:[[NSBundle mainBundle] pathForImageResource:@"fundoJanela.tif"]];
	[fundoDX setSize:NSMakeSize(325.0,81.0)];
	
	[self setNeedsDisplay:YES];
}

- (void)drawRect:(NSRect)aRect
{
	[fundoDX compositeToPoint:NSZeroPoint fromRect:[self frame] operation:NSCompositeCopy];
}

- (BOOL)movableByWindowBackground
{
	return YES;
}

- (BOOL)isFlipped
{
	return NO;
}

</source>

DXCampoTexto (Subclass of NSTextField)
<source lang="objc">
#import "DXCampoTexto.h"

@implementation DXCampoTexto

- (void)awakeFromNib
{
	[self setBackgroundColor:[NSColor whiteColor]];
	[self setBordered:YES];
	self window] performSelector:@selector(makeFirstResponder:) withObject:self afterDelay:0.0];
}

- (BOOL)isEditable
{
	return YES;
}

- (BOOL)isEnabled
{
	return YES;
}

@end

</source>

Basically, [[DXFloatingWindow is the main application window (borderlerss, floating window), and DXQuadroJanela is a view inside it that takes all of the space. It loads an image that is used as the window background (Tiff Image with Alpha Channel @ 32bits). DXCampoTexto is a custom NSTextField that is placed on top of DXQuadroJanela in the window nib.
What happens is that I can see the window, drag it around, and the background image is perfect. But any controls i add to the window (custom or not) refuse to let me select their content (in this case the text/string of NSTextField).

FernandoLucasSantos

----

** 

Note that borderless windows don't become key by default. Override - (BOOL)canBecomeKeyWindow method in DXFloatingWindow.m 
to make it work correctly.

**

----

Ok i'm tearing my hair out over this one:

I have a borderless window that I created by subclassing NSWindow and specifying NSBorderlessWindowMask - all works fine. I overrode the (BOOL)canBecomeKeyWindow method and have checked that my window can indeed become key by checking with the canBecomeKey method. I then created a delegate method for - (BOOL)windowDidResignKey:(id)sender which effectivly fades the window out of existence with a timer and setting the opacity when the user clicks out of the window. The problem that I have is that no matter what I try when I try to fade the window back in and set it to be the key window I cannot get the window to become key. It becomes the key window fine when I click on it. I check this by creating a - (BOOL)windowDidBecomeKey:(id)sender delegate. I've tried makeKeyAndOrderFront: and makeKeyWindow and every combination I can think of.

I know that there are Key Window issuse with borderless windows and I may be missing something here but it must be possible - has anyone got any pointers for me?

*I had serious problems with status-level windows being loaded from a bundle for a long time. It was nothing like your situation, except that the windows would not become key until I clicked on them. Just recently, however, I changed them all to use an NSWindowController for each one, and the problem went away. Immediately. So if I were you, the first thing I would try is to use an NSWindowController (or subclass) to both load the nibs and serve as the File's Owner. Also, only put one window in each nib, and make sure it's connected to the window controller's     window outlet. Finally, you'll have to use     showWindow: instead of     makeKeyAndOrderFront: from now on. Good luck! --JediKnil*

Thanks for that, i'll give it a try - I actually started to think about doing it that way myself this morning.

----

** 

Also note, borderless windows may not contain standard toolbars (NSToolbar).

**

----

When calling the setToolbar: method, you'll get an error similar to:

*
ERROR: Can't have a toolbar in a window with <NSNextStepFrame: 0x341020> as it's borderview
*

----

**You can do it IB by making it a custom class...**

TransparentWindow.h
<source lang="objc">

@interface TransparentWindow : NSWindow
{}

-(id) initWithContentRect: (NSRect) contentRect 
			styleMask: (unsigned int) styleMask 
			   backing: (NSBackingStoreType) backingType 
				defer: (BOOL) flag;


@end
</source>
TransparentWindow.m
<source lang="objc">

@implementation TransparentWindow

-(id) initWithContentRect: (NSRect) contentRect 
				styleMask: (unsigned int) styleMask 
				  backing: (NSBackingStoreType) backingType 
					defer: (BOOL) flag {

if ((self = [super initWithContentRect:contentRect
		                           styleMask: NSBorderlessWindowMask 
                                              backing:backingType
		                                   defer: flag])) {
		/* May want to setup some other options, 
                    like transparent background or something */
	}

	return self;
}

</source>
Note, this has been quickly adapted from a much larger piece of code.
----

Is the window set to be 'visible at launch time' in the nib?

----

Is it possible to disable the title bar (the bar with the minimize/restore and zoom buttons) on an NSWindow?  I dont mean just to disable the buttons, but  to remove the whole bar from the window.

----

NSBorderlessWindowMask is definitely the way to go. You can't specify that in Interface Builder, though, so you have to create the window programmatically. In most cases, you still want to use IB to arrange the contents of the window, so create an empty view in your nib and put everything in there. Create the window in your code using 
    [NSWindow initWithContentRect:styleMask:backing:defer:]
and specifying NSB**'orderlessWindowMask in the styleMask parameter. Load the nib (if you haven't already) and send the window setContentView to set the view in the nib as the content view of the window.

Or you could just make a subclass. Just don't get carried away with putting stuff in the subclass. Much of what you'd put in there probably belongs in the window's delegate or controller.

----

How can I add resizing to a window set to NSB**'orderlessWindowMask?

----

One thing you may need to do is learn how to do online research. I saw this tantalizing fragment in a google search, but the mamasam page would not load (Google does give you a lot of useless crap, and then saves information about you into the bargain!):

*In an NSWindow subclass you can listen for the mouse ... bottom right corner then listen for dragging - resize the window ... in my app I have a borderless window, as ...*

long gone, i guess

aha! here it is: http://www.cocoabuilder.com/archive/message/cocoa/2004/2/18/97332

----

I have seen that, and other similar references, unfortunately I need a bit more meat on those bones... I understand the concept, I just haven't managed to get it to work.

----

I have absolutely no idea what level of expertise you are starting with, so I am afraid I cannot recommend much in the way of practice projects to ease your way into this. What have you scrounged up in the way of information on "subclassing NSWindow"? Quite frankly, I don't think anyone is going to jot down the code for you here. They might try to sell you some code, but you wouldn't know if it was *their* code or code that somebody gave them for free, if you catch my drift.

----

Yeah, I hear ya. 

I was just hoping for something more specific. As it turns out, I managed to get it working. It does pretty much what is described in the above referenced archived Cocoa-Dev message. I stopped trying to work with a tracking rect and just evaluated based on locationInWindow, it was simpler.

One of the problems I was having was that I wanted to implement this in my custom view, since it does all the drawing and I wanted to be able to set a single flag that would both show a resize widget and allow resizing. The view is in a transparent window and things were acting squirrelly until I realized that it would be easier to resize the window and let my view inherit the changes as opposed to resizing the view. Thanks for the time!

----

I had a little trouble getting a resizeable and moveable borderless view to work recently... i botched up my screen/window coordinates when trying to pin the top left hand corner. For posterity, here's my code in my NSWindow subclass to achieve this:

<source lang="objc">

//Once the user starts dragging the mouse, we move the window with it. We do this because the window has no title
//bar for the user to drag (so we have to implement dragging ourselves)
- (void)mouseDragged:(NSEvent *)theEvent
{
	NSPoint currentLocation;
	NSPoint newOrigin;
	NSRect  screenFrame = [[NSScreen mainScreen] frame];
	NSRect  windowFrame = [self frame];
	
	// 1. Is the Event a resize drag (test for bottom right-hand corner)?
	if (mouseDownType == PALMOUSEDRAGSHOULDRESIZE) {
		// i. Remember the current downpoint
		NSPoint currentLocationOnScreen = [self convertBaseToScreen:[self mouseLocationOutsideOfEventStream]];
		currentLocation = [theEvent locationInWindow];
		
		// ii. Adjust the frame size accordingly
		windowFrame.size.width = initialFrame.size.width + (currentLocation.x - initialLocation.x);
		float heightDelta = (currentLocationOnScreen.y - initialLocationOnScreen.y);
				
		windowFrame.size.height = (initialFrame.size.height - heightDelta);
		windowFrame.origin.y = (initialFrame.origin.y + heightDelta); 
		
		// iii. Set
		[self setFrame:windowFrame display:YES animate:NO];
	}
    else {
		//grab the current global mouse location; we could just as easily get the mouse location 
		//in the same way as we do in -mouseDown:
		currentLocation = [self convertBaseToScreen:[self mouseLocationOutsideOfEventStream]];
		newOrigin.x = currentLocation.x - initialLocation.x;
		newOrigin.y = currentLocation.y - initialLocation.y;
		
		// Don't let window get dragged up under the menu bar
		if( (newOrigin.y+windowFrame.size.height) > (screenFrame.origin.y+screenFrame.size.height) ){
			newOrigin.y=screenFrame.origin.y + (screenFrame.size.height-windowFrame.size.height);
		}
		
		//go ahead and move the window to the new location
		[self setFrameOrigin:newOrigin];
		
	}
}

//We start tracking the a drag operation here when the user first clicks the mouse,
//to establish the initial location.
- (void)mouseDown:(NSEvent *)theEvent
{    
    //grab the mouse location in global coordinates
	initialLocation = [theEvent locationInWindow];
	initialLocationOnScreen = [self convertBaseToScreen:[theEvent locationInWindow]];
	
	initialFrame = [self frame];
	
	// What should a drag event do, if started from here?
	if (initialLocation.x > initialFrame.size.width - 20 && initialLocation.y < 20) {
		mouseDownType = PALMOUSEDRAGSHOULDRESIZE;
	}
	else {
		mouseDownType = PALMOUSEDRAGSHOULDMOVE;
	}
}

</source>

----

There's a flaw in the code directly above.  I've been unable to locate it.  Perhaps someone better acquainted with it could give it a try.

I found the problem, or at least possible problem.  Instead of using...
    
mouseDownType = PALMOUSEDRAGSHOULDRESIZE;


just use a BOOL 

    
mouseDragShouldResize = YES


You can just set that and the feature works much better.

----
Does anyone know of any sample code that resizes a window with the same behavior as a standard NSWindow? (IE. the resizing isn't jumpy, and the left origin actually stays anchored to the left)?

++ Falco

----
Ok, in my sleep deprivation i didn't realize that my window was set to be moveable by the background, so it was trying to move the window at the same time that the resize code was resizing the window.

++ Falco
----
Here's a revised edition of above's code. I found it not to react to mouseDown, but to mouseUp, so I edited it accordingly. Also, you can't resize the image smaller than (323,288). You can change that to your liking.

<source lang="objc">
- (void)mouseDragged:(NSEvent *)theEvent
{
	if (shouldRedoInitials)
	{
		initialLocation = [theEvent locationInWindow];
		initialLocationOnScreen = [self convertBaseToScreen:[theEvent locationInWindow]];
		
		initialFrame = [self frame];
		shouldRedoInitials = NO;
		
		if (initialLocation.x > initialFrame.size.width - 20 && initialLocation.y < 20) {
			shouldDrag = NO;
		}
		else {
			//mouseDownType = PALMOUSEDRAGSHOULDMOVE;
			shouldDrag = YES;
		}
		
		screenFrame = [[NSScreen mainScreen] frame];
		windowFrame = [self frame];
		
		minY = windowFrame.origin.y+(windowFrame.size.height-288);
	}
	
	
	// 1. Is the Event a resize drag (test for bottom right-hand corner)?
	if (shouldDrag == FALSE)
	{
		// i. Remember the current downpoint
		NSPoint currentLocationOnScreen = [self convertBaseToScreen:[self mouseLocationOutsideOfEventStream]];
		currentLocation = [theEvent locationInWindow];
		
		// ii. Adjust the frame size accordingly
		float heightDelta = (currentLocationOnScreen.y - initialLocationOnScreen.y);
		
		if ((initialFrame.size.height - heightDelta) < 289)
		{
			windowFrame.size.height = 288;
			//windowFrame.origin.y = initialLocation.y-(initialLocation.y - windowFrame.origin.y)+heightDelta;
			windowFrame.origin.y = minY;
		} else
		{
			windowFrame.size.height = (initialFrame.size.height - heightDelta);
			windowFrame.origin.y = (initialFrame.origin.y + heightDelta);
		}
		
		windowFrame.size.width = initialFrame.size.width + (currentLocation.x - initialLocation.x);
		if (windowFrame.size.width < 323)
		{
			windowFrame.size.width = 323;
		}
		
		// iii. Set
		[self setFrame:windowFrame display:YES animate:NO];
	}
    else
	{
		//grab the current global mouse location; we could just as easily get the mouse location 
		//in the same way as we do in -mouseDown:
		currentLocation = [self convertBaseToScreen:[self mouseLocationOutsideOfEventStream]];
		newOrigin.x = currentLocation.x - initialLocation.x;
		newOrigin.y = currentLocation.y - initialLocation.y;
		
		// Don't let window get dragged up under the menu bar
		if( (newOrigin.y+windowFrame.size.height) > (screenFrame.origin.y+screenFrame.size.height) )
		{
			newOrigin.y=screenFrame.origin.y + (screenFrame.size.height-windowFrame.size.height);
		}
		
		//go ahead and move the window to the new location
		[self setFrameOrigin:newOrigin];
		
	}
}

- (void)mouseUp:(NSEvent *)theEvent
{
	shouldRedoInitials = YES;
}
</source>

What you'll need to declare in your .h file:
<source lang="objc">
BOOL shouldDrag;
BOOL shouldRedoInitials;
NSPoint initialLocation;
NSPoint initialLocationOnScreen;
NSRect initialFrame;
NSPoint currentLocation;
NSPoint newOrigin;
NSRect screenFrame;
NSRect windowFrame;
float minY;
</source>
kind regards,
Matthias
----
Is there any way to close a borderless window? I suppose that would be good for creating your own close buttons.

----
The answer to that question seems kind of obvious. "Close" just means to remove from the screen. If you couldn't close them then they would stay visible forever.

----
Of course there is.  Just wire up an NSButton to the window's orderOut: action.
----
I feel really stupid. I didn't remeber that that action existed.  And you can't use the close or performClose: actions because there is no title bar in an NSBorderlessWindowMask.  I mean I was referring to making the window disappear while running the application.

I have changed the above code a little to cater only move (if somebody want to implement the move only).  
http://the-useful.blogspot.com/2011/08/draggable-transparent-nswindow.html

