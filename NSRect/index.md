---
layout: page
title: NSRect
---

Foundation's NSRect structure, defined in NSGeometry.h.

    
typedef struct  {
    NSPoint origin;
    NSSize size;
} NSRect;


The NSRect struct differs from QuickDraw's Rect struct in two important ways:



* The NSRect struct is a rectangle defined by an origin expressed as coordinates and by its height and width. QuickDraw's Rect defines all four sides of its rectangle as coordinates. Having used both systems for a while I do find the origin/size strategy a little more convenient than the four-coordinates strategy -- but one can convert between the two with ease.

* The elements of NSRect (origin.x, origin.y, size.width, size.height) are all float types. QuickDraw's Rect coordinates are all integer numbers. Rect was designed in a time when integer math was vastly faster and addressing individual pixels was all that could be done, whereas Cocoa and CoreGraphics work in a resolution-independent space with the ability to address the areas between pixels.



NSGeometry.h defines a number of helper functions for working with NSRect structs. Some commonly used functions include:

    

* NSRect NSMakeRect(float x, float y, float w, float h)
* NSRect NSRectFromString(NSString *aString)
* BOOL NSEqualRects(NSRect aRect, NSRect bRect)
* NSRect NSIsEmptyRect(NSRect aRect)
* NSRect NSInsetRect(NSRect aRect, float dX, float dY)
* NSRect NSUnionRect(NSRect aRect, NSRect bRect)
* NSRect NSIntersectionRect(NSRect aRect, NSRect bRect)
* NSString *NSStringFromRect(NSRect aRect)



NSStringFromRect is most handy when used in conjunction with NSLog for printing debug messages:

    
NSLog(@"frameRect = %@", NSStringFromRect(frameRect));


Note that unlike Carbon API all of these API pass the entire structure on the stack. In Carbon, one typically passes the address of a Rect around. Don't let that trip you up.

Foundation also defines the global variable:

    
NSZeroRect


Which is occasionally useful when performing set arithmetic with the aforementioned functions and the like.

----
*Discussion*

I cleaned up the page a bit to incorporate the bit about "Carbon's Rect" being incorrect or ambiguous. I also reworked the part that talked about how Rect was faster because it used ints, since integer math hasn't been faster than floating point math for well over a decade.

