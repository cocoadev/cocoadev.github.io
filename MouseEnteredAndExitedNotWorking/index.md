---
layout: page
title: MouseEnteredAndExitedNotWorking
---

I have a subclassed NSButton, and I can't get mouseEntered: and mouseExited: to work, though mouseDown: does work. I'd post my code, but there isn't really anything to show, since the only other method I have is drawRect:. Unless drawRect: could have an effect on the mouse tracking?
If anyone has any idea of what could be wrong, please help.
Thanks.

-- Eamon

----

When in doubt, read the documentation. In this case, the documentation says:

*Informs the receiver that the cursor has entered a tracking rectangle.*

Have you, in fact, set up a tracking rectangle?

----

Aha, that was the problem, thanks. I guess I bypassed the addTrackingRect:etc: method because I've done this before without using it. I don't know how... Oh well.

----

There is also a     - (BOOL)acceptsMouseMovedEvents method, which you must override in your NSWindow subclass to receive such events.
If you want to implement simple tracking of entire bounds of your control, implement something like this:

    

//Add this instance variable to your view interface:
   NSTrackingRectTag            tracking_tag;

- (id) initWithFrame:(NSRect)rect {
  /* your init here */
  	[[NSNotificationCenter defaultCenter] addObserver:self
			selector:@selector(frameDidChange:)
			 name:NSViewFrameDidChangeNotification
			  object:self];
}

- (void)viewDidMoveToWindow { [self addTrackingRect]; }
- (void)frameDidChange:(NSNotification *)aNotif { [self addTrackingRect]; }

- (void) addTrackingRect {
	if ( tracking_tag ) [self removeTrackingRect:tracking_tag];
	tracking_tag = [self addTrackingRect:NSMakeRect( 0, 0, [self frame].size.width, [self frame].size.height ) owner:self userData:nil assumeInside:NO];
}

//mouseEntered: and mouseExited: methods goes here. Don't forget to call [super ...] inside them.



-- DenisGryzlov

----

Okay, cool, thanks.

