---
layout: page
title: GettingTheColorUnderTheCursor
---

Hi everyone,
I'm trying to get the color under the cursor without an NSColorWell or Panel.
For instance, if there is a blue square on my screen and I hover (or click) it, I'll get a "blue" NSColor.
I've seen this done in apps like xscope <http://iconfactory.com/xs_home.asp>
I have no idea how to do this, any pointers would be good :).

Thanks for helping me out.
- Brian

----

Well, I'll give a pointer - NSReadPixel, which is a function defined by the AppKit.  I'm not sure of the details though, so maybe you'll tell us the details when you get it to work?  (In particular, you may or may not have to use a screen(s) spanning transparent window in order to get a view.)

----
Hmm.. I think I'll have to use a transparent window since it needs a view.. but arent totally transparent windows unable to be clicked?
And how would I get this window to span the whole screen and be on top of everything?
Heres a sample..

<code>
NSColor  *pixelColor;

[self lockFocus];  // NSReadPixel pulls data out of the current focused graphics context, so -lockFocus is necessary here.

pixelColor = NSReadPixel(pixelPosition); //pixelPosition is of type NSPoint and has the value of the position where you want to read the color

[self unlockFocus];  //

</code>

I would need to change self to the transparent window that covers the whole screen.. or is there another way..?

----

Just track the mouse with     [NSEvent mouseLocation] and then use that point with some code over from ScreenShotCode.

----

Here's a short and sweet class category for NSColor which will return the color for a given NSPoint.

    // .h file
@interface NSColor (ColorOnScreen)

+ (NSColor *)colorOnScreenAtPoint:(NSPoint)point;
+ (NSColor *)colorFromRGBColor:(RGBColor)color;

@end

// .m file
@implementation NSColor (ColorOnScreen)

+ (NSColor *)colorOnScreenAtPoint:(NSPoint)point
{
	RGBColor color;
	NSPoint newPoint;
	
	// Move the origin point to the top left instead of the bottom left
	newPoint = point;
	newPoint.y = [[NSScreen mainScreen] frame].size.height - point.y;
	
	// Here's where the magic happens. This grabs the color of the screen at a specific point
	GetCPixel(newPoint.x, newPoint.y, &color);
	
	return [self colorFromRGBColor:color];
}

+ (NSColor *)colorFromRGBColor:(RGBColor)color
{
	float red, green, blue;
	
	// 65535 is the max for the color
	// so we divide it by that to find the float value (0.0-1.0)
	red = (float)color.red/65535;
	green = (float)color.green/65535;
	blue = (float)color.blue/65535;
	
	return [self colorWithCalibratedRed:red green:green blue:blue alpha:1.0];
}

@end


The origin (0,0) point is at the bottom left of the screen. The method uses the QuickDraw function GetCPixel() so you may need to add the framework if you get compilation errors. Here's an example on how to use it:

    NSColor *color;
color = [NSColor colorOnScreenAtPoint:[NSEvent mouseLocation]];


Have fun! -- RyanBates

*Unfortunately, QuickDraw is deprecated in Mac OS X 10.4.  So an alternate approach might be better.*

Quickdraw is deprecated precisely because something like this is possible: for QD to read the pixel, the Compositing Manager has to flatten the whole screen, read the pixel (from video mem if QE is used), and then continue. That's expensive.

----

Actually Ryans Code is extremely fast compared to this
    - (NSColor *)colorOnScreenAtPoint:(NSPoint)point
{
	NSRect screenRect = [[[NSScreen screens] objectAtIndex:0] frame]; 
	NSWindow *window = [[NSWindow alloc] initWithContentRect:screenRect 
												   styleMask:NSBorderlessWindowMask backing:NSBackingStoreNonretained 
													   defer:NO];
	[window setLevel:NSScreenSaverWindowLevel + 100]; 
	[window setHasShadow:NO]; 
	[window setAlphaValue:0.0];
	[window orderFront:self];
	[window setContentView:[[[NSView alloc] initWithFrame:screenRect] autorelease]];
	window contentView] lockFocus];
    [[NSColor *pixelColor = NSReadPixel(point); //point is of type NSPoint and has the value of the position where you want to read the color
    [[window contentView] unlockFocus];
	[window orderOut:self]; 
	[window close]; 
	return pixelColor;
}


*That method also leaks memory badly... "window" is never released.* 

**Actually, closing the window releases it.**

Yes, Ryans code is fast, but using the same paradigm everywhere (as QD does) isn't. After all, there's no other way to find the color of a pixel than by compositing everything together.

