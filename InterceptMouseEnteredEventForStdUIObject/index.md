---
layout: page
title: InterceptMouseEnteredEventForStdUIObject
---

How do I get a standard UI object like NSButton to respond to "mouse entered" events in a custom manner?

(I know that in direct NSView subclasses you override the mouseEntered: method from NSResponder.)

*NSButton will work the same way because it is a subclass of NSView.*

----

Am I on the right track if I subclass and override initWithFrame:, add a tracking rectangle there, and then
implement the appropriate mouseThisAndThat methods? Or is this approach needlessly Byzantine?

----

Yes, that is the right approach. However, you will need to manually update the tracking rect when the view is resized or moved because it is actually managed by the window. There is a lot of information about tracking in the docs: http://developer.apple.com/documentation/Cocoa/Conceptual/BasicEventHandling/Tasks/HandlingMouseTracking.html

If all you want to do is display a button's border while the mouse is over it, simply use NSButton's setShowsBorderOnlyWhileMouseInside: method.

-- RyanBates

----

Is initWithFrame: called if the object is unarchived from a NIB?

(*This question was not asked by the OP (writing here, to clarify) but is one he has also.)*

----

Well, yes and no. In short, it is only called when you use the "Custom View" in IB - but not in any other circumstances. See this for details: http://developer.apple.com/documentation/Cocoa/Conceptual/LoadingResources/Concepts/NibFileLoaded.html

I would just reset the tracking rects in the setFrameOrigin:, setFrameSize: and viewDidMoveToWindow NSView methods. I believe this covers all the circumstances where the tracking rect needs to be set, so don't bother setting it in initWithFrame: or awakeFromNib.

-- RyanBates

----

A whimsical example:

S<nowiki/>neakyButton.h

    
#import <Cocoa/Cocoa.h>

@interface SneakyButton: NSButton
{
	NSTrackingRectTag trackingRect;
}

@end


S<nowiki/>neakyButton.m

    
#import "SneakyButton.h"

@implementation SneakyButton

- ( void ) awakeFromNib
{
	[ [ self window ] setAcceptsMouseMovedEvents: YES ];
}

- ( void ) viewDidMoveToWindow
{
	trackingRect = [ self addTrackingRect: [ self visibleRect ]
		owner: self userData: nil assumeInside: NO ];
}

- ( BOOL ) acceptsFirstResponder
{
	return YES;
}

- ( void ) mouseEntered: ( NSEvent * ) theEvent
{
	int x = abs( ( rand( ) % ( int )[ [ self superview ] frame ].size.width ) - 100 );
	int y = abs( rand( ) % ( int )[ [ self superview ] frame ].size.height - 25 );
	
	[ self setFrameOrigin: NSMakePoint( x, y ) ];
	[ self removeTrackingRect: trackingRect ];
	[ [ self superview ] setNeedsDisplay: YES ];
	trackingRect = [ self addTrackingRect: [ self visibleRect ]
		owner: self userData: nil assumeInside: NO ];
}

- ( void ) mouseExited: ( NSEvent * ) theEvent
{
	[ [ self window ] setAcceptsMouseMovedEvents: NO ];
}

@end


----

I just posted some code for how to do this while handling resizing interfaces at the AddTrackingRect page.

-- WAHa

