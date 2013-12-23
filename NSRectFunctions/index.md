---
layout: page
title: NSRectFunctions
---



Functions and constants for NSRect.

    
NSPoint  UKCenterOfRect( NSRect rect )
{
	return NSMakePoint( NSMidX(rect), NSMidY(rect) );
}

NSPoint  UKTopCenterOfRect( NSRect rect )
{
	return NSMakePoint( NSMidX(rect), NSMaxY(rect) );
}

NSPoint  UKTopLeftOfRect( NSRect rect )
{
	return NSMakePoint( NSMinX(rect),NSMaxY(rect) );
}

NSPoint  UKTopRightOfRect( NSRect rect )
{
	return NSMakePoint( NSMaxX(rect), NSMaxY(rect) );
}

NSPoint  UKLeftCenterOfRect( NSRect rect )
{
	return NSMakePoint( NSMinX(rect), NSMidY(rect) );
}

NSPoint  UKBottomCenterOfRect( NSRect rect )
{
	return NSMakePoint( NSMidX(rect), NSMinY(rect) );
}

NSPoint  UKBottomLeftOfRect( NSRect rect )
{
	return rect.origin;
}

NSPoint  UKBottomRightOfRect( NSRect rect )
{
	return NSMakePoint( NSMaxX(rect), NSMinY(rect) );
}

NSPoint  UKRightCenterOfRect( NSRect rect )
{
	return NSMakePoint( NSMaxX(rect), NSMidY(rect) );
}

NSRect NKScaleRect(NSRect inRect, float scaleX, float scaleY)
{	
	NSRect outRect = inRect;
	outRect.size.width  += scaleX * inRect.size.width;
	outRect.size.height += scaleY * inRect.size.height;
	
	outRect.origin.x -= (outRect.size.width - inRect.size.width) / 2.0;
	outRect.origin.y -= (outRect.size.height - inRect.size.height) / 2.0;
	
	return outRect;
}


----

Based on code by John C. Randolph, but prettied up, renamed and debugged.

----

Shouldn't this page be named UKRectFunctions?

----

Wow, one would want to go through the trouble of linking against a library with these functions? So,     UKRightCenterOfRect(rect) is that much faster to type then     NSMakePoint(NSMaxX(rect), NSMidY(rect))? And I didn't even use     UKBottomLeftOfRect() as an example!!

Silly if you ask me...
----
It's not that they're that much faster to write, it's that they're faster to *read*.  When you see "bottom left", it's a tad more obvious than NSMaxX()/NSMidY().  

----

You could just #import "UKRectFunctions.h"

----

Syntax help: No 
Explanation: No 
How to use: No 
 ---Perhaps this page is useless...

*Perhaps you need to learn how to use a C function.*
----
Also, I think these functions are sort of wrong!     NSMax(r) returns     NSMinX(r) + NSWidth(r). When I ask for the top-right point I expect to get     NSMinX(r) + NSWidth(r) - 1, but maybe that is just me?
----
It's consistent with how     NSMaxRange() works. Apple sez: *Returns range.location + range.length�in other words, the number 1 greater than the maximum value within the range.*
----
I was referring to the UK-functions, not     NSMaxX. Is the top right corner one greater than the corner inside the view? I guess things get more complicated by the fact that the view coordinate system doesn't really match the screen coordinate system, so filling a single pixel in the top right corner would anyway not be as simple as using the actual coordinate for that place ;)

