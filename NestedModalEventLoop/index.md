---
layout: page
title: NestedModalEventLoop
---



I'm trying to implement menu-like behaviour using a pop-up window. It 99% works, but there's one thing causing problems. I will usually want to pop up a menu on a mouseDown event in some view. Having figured out where to place the "menu" window, its size and all that, I hand over to its own internal event tracking method. That implements a modal loop which basically hands out mouseDown, dragged and mouseUp events to a nominated view inside that window. The idea is that you could put any view here and it will be none the wiser - it will just get mouse events as usual.

My problem is that when I go from the "outer" mouseDown event handler to my tracking loop, the mouse location values I receive are for the parent window, not for the new popped-up window. My second level tracking loop operates in two ways, to mimic usual menu behaviour - if I click once to pop it up, then click-drag-click to work on its contents, all is well. If I hold the mouse down and drag through the new window, I get the parent window coordinates. I've tried everything I can think of to let the window server know I've switched into a new window (makeMainWindow, flushing events, etc), but unless the mouse is released, it never seems to notice. This has to be doable, as real pop-up menus must do the same.

Any help on this would be gratefully received. --GrahamCox.

Essence of the code posted below. Note that the test view I add is unimportant - any view will do but the test view makes it easy to see where I think my
mouse location is.

    

@interface GCWindowMenu : NSWindow
{
	IBOutlet	NSView*		_mainView;
}

+ (void)			popUpWindowMenu:(GCWindowMenu*) menu withEvent:(NSEvent*) event forView:(NSView*) view;
+ (GCWindowMenu*)	windowMenu;
+ (GCWindowMenu*)   windowMenuWithContentView:(NSView*) view;


- (void)			trackWithEvent:(NSEvent*) event;

- (void)			setMainView:(NSView*) aView sizeToFit:(BOOL) stf;
- (NSView*)			mainView;

@end



#define kGCDefaultWindowMenuSize  (NSMakeRect(0, 0, 100, 28 ))


//=====================================================================================



#import "GCWindowMenu.h"

#define __MAKE_TEST_VIEW__   0


+ (void)	popUpWindowMenu:(GCWindowMenu*) menu withEvent:(NSEvent*) event forView:(NSView*) view
{
	// pop up a window menu, and track it until the mouse goes up. Implements standard menu behaviour
	// but uses a completely custom view. If <menu> is nil creates a default window. <event> is usually a
       // mouseDown in <view>

		
	if ( menu == nil )
		menu = [GCWindowMenu windowMenu];
	
	[menu retain];
	
	// figure out where to show it
	
	NSPoint loc = [event locationInWindow];
	loc = event window] convertBaseToScreen:loc];
	
	loc.x -= 10;
	loc.y -= 5;
	
	[menu setFrameTopLeftPoint:loc];
	
	// show the "menu"
	
	[menu setParentWindow:[view window;
	view window] addChildWindow:menu ordered:[[NSWindowAbove];
	[menu orderFront:self];
	//[menu makeFirstResponder:menu];
	//[menu makeMainWindow];
	
	// track the menu (keeps control in its own event loop):

	[menu trackWithEvent:event];
	
	// all done, tear down
	
	view window] removeChildWindow:menu];
	[menu orderOut:self];
	
	[menu release];
	
	[[NSLog(@"pop-up complete");
}



+ (GCWindowMenu*)	windowMenu
{
	GCWindowMenu* fi =  [[GCWindowMenu alloc]  initWithContentRect:NSZeroRect
												styleMask:NSBorderlessWindowMask
												backing:NSBackingStoreBuffered
												defer:YES];
	
	// note - because windows are all sent a -close message at quit time, set it
	// not to be released at that time, otherwise the release from the autorelease pool
	// will cause a crash due to the stale reference

	[fi setReleasedWhenClosed:NO];	// ****' important!! ****'
	
	#if __MAKE_TEST_VIEW__
	
	NSRect sr = NSMakeRect( 0, 0, 230, 230 );
	NSView* test = [[GCColourPickerView alloc] initWithFrame:sr];
	
	[fi setMainView:test sizeToFit:YES];
	[test release];
	
	#endif
	
	return [fi autorelease];
}



+ (GCWindowMenu*)   windowMenuWithContentView:(NSView*) view
{
	GCWindowMenu* menu = [self windowMenu];
	
	[menu setMainView:view sizeToFit:YES];
	return menu;
}


- (id)	initWithContentRect:(NSRect) contentRect
		styleMask:(unsigned int) styleMask
		backing:(NSBackingStoreType) bufferingType
		defer:(BOOL) deferCreation
{
	if ((self = [super initWithContentRect:contentRect
						styleMask:styleMask
						backing:bufferingType
						defer:deferCreation]) != nil )
	{
		[self setLevel:NSNormalWindowLevel];
		[self setHasShadow:YES];
		[self setOpaque:YES];
		[self setReleasedWhenClosed:YES];
		[self setFrame:kGCDefaultWindowMenuSize display:NO];
		
		_mainView = nil;
	}
	
	return self;
}



- (void)			trackWithEvent:(NSEvent*) event
{
	// tracks the "menu" by keeping control until a mouse up (or down, if menu 'clicked' into being)
	
	NSLog(@"starting tracking..., event = %@", event);
		
	//[NSEvent startPeriodicEventsAfterDelay:1.0 withPeriod:0.1];
	
	self mainView] mouseDown:event];
	
	[[NSEvent* theEvent;
	BOOL keepOn = YES;
	unsigned int mask;
	BOOL invertedTracking = NO;
	
	mask = NSLeftMouseUpMask | NSLeftMouseDraggedMask | NSRightMouseUpMask | NSRightMouseDraggedMask | NSPeriodicMask;
 
	while (keepOn)
	{
		theEvent = [self nextEventMatchingMask:mask];

		switch ([theEvent type])
		{
			case NSMouseMovedMask:
			case NSRightMouseDragged:
			case NSLeftMouseDragged:
				self mainView] mouseDragged:theEvent];
				break;
			
			case [[NSRightMouseUp:
			case NSLeftMouseUp:
				// if this is within a very short time of the mousedown, leave the menu up but track it
				// using mouse moved and mouse down to end.
				
				if ([theEvent timestamp] - [event timestamp] < 0.25 )
				{
					invertedTracking = YES;
					mask = NSLeftMouseDownMask | NSLeftMouseDraggedMask | NSRightMouseDownMask | NSRightMouseDraggedMask | NSMouseMovedMask | NSPeriodicMask;
				}
				else
				{
					self mainView] mouseUp:theEvent];
					keepOn = NO;
				}
				break;
				
			case [[NSRightMouseDown:
			case NSLeftMouseDown:
				if ( ! NSPointInRect([theEvent locationInWindow], self mainView] frame]))
					keepOn = NO;
				else
					[[self mainView] mouseDown:theEvent];
				break;

			case [[NSPeriodic:
				break;

			default:
				/* Ignore any other kind of event. */
				break;
		}
	}
	
	[self discardEventsMatchingMask:NSAnyEventMask beforeEvent:theEvent];

		
	//[NSEvent stopPeriodicEvents];
	NSLog(@"ending tracking...");
}


- (BOOL)			canBecomeMainWindow
{
	return YES;
}


- (void)			setMainView:(NSView*) aView sizeToFit:(BOOL) stf
{
	[aView retain];
	[_mainView release];
	_mainView = aView;
	
	// add as a subview
	
	self contentView] addSubview:aView];
	
	// if stf, position the view at top, left corner of the window and
	// make the window the size of the view
	
	if ( stf )
	{
		[[NSRect fr = [aView frame];
	
		fr.origin = NSZeroPoint;
		[aView setFrameOrigin:NSZeroPoint];
		[self setFrame:fr display:YES];
	}
	
	[_mainView setNeedsDisplay:YES];
}


- (NSView*)			mainView
{
	return _mainView;
}



@end




----

I have found one solution, but it strikes me as very hackish. I can detect the bad events because [event window] ! == self. In that case I can remake the event using a converted point that goes from windowA->global->windowB. Still looking for a cleaner approach if possible! -GC.

----

Random guesses - try sending an NSEvent enterExitEventWithType, or look at NSView removeTrackingRect.

