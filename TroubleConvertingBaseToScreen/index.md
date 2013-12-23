---
layout: page
title: TroubleConvertingBaseToScreen
---



I'm writing a method to return a point (in screen coordinates) relative to a view in my application, so I can position a second window there. This should be simple stuff, but for some reason I'm having trouble getting the proper coordinates from the view. The view hierarchy in my application looks a little like this:

    
General/NSWindow
Window's contentView
General/NSSplitView
General/NSTabView
Custom View


In order to convert a point from Custom View to screen coordinates, I have this method:

    
- (General/NSPoint)screenOriginForWindow;
{
	General/NSView *view = General/tabView selectedTabViewItem] view];
	if ( !view )
		return [[NSZeroPoint;

	float x = General/NSMinX( [view frame] );
	float y = [view isFlipped] ? General/NSMinY( [view frame] ) : General/NSMaxY( [view frame] );
	General/NSPoint origin = [view convertPoint:General/NSMakePoint( x, y ) toView:nil];

	return General/self window] convertBaseToScreen:origin];
}


From reading the documentation it seems this should work, but it does not. The [[NSSplitView and the Window's contentView work correctly, but anything else seems to be positioned wrong, as if     convertPoint isn't doing it's job, or the split view is messing up the frame origin on its subviews somehow. Does anyone have an idea how I'm messing this up?

----

You should convert the point in two steps:


*
1. Convert from view to window coordinates
*
2. Convert from window to screen coordinates


Given a point p inside a view v (p must be in terms of v's bounds, not its frame!):

    
General/NSPoint windowPoint = [v convertPoint:p toView:nil];
General/NSPoint screenPoint = General/v window] convertBaseToScreen:windowPoint];


Or do it all in one line:

    
[[NSPoint screenPoint = General/v window] convertBaseToScreen:[v convertPoint:p toView:nil;


----
Thank you for your reply. The problem in my code was that I wasn't using the bounding rectangle to create the point-- I didn't realize the frame rectangle wouldn't work!
