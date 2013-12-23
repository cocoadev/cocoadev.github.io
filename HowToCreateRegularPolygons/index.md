---
layout: page
title: HowToCreateRegularPolygons
---



Alright, I've been trying to create a varity of regular polygons but can't seem to do it. However, I have found a site which tells how to do it in C++ (i think). But, I don't seem to be able to do what it says using NSBezierPath... Can anyone help?

Heres the site: http://homepage.cs.uri.edu/courses/csc406/Assignments/Prog01/prog01.html
(scroll down till you see the graphics)
-- JohnDevor

Well, this doesn't seem too difficult. What I imagine would be the simplest course of action would be to create all the points that are going to be your verticies first, then iterate through them all and use NSBezierPath's lineToPoint: method to draw from line to line and then close it off when you've gone through all the points.

So the trickiest part would be finding all the points for your verticies. I believe if you create a function that takes in the center point of the circle described at the website above, its radius, and the number of sides of the desired polygon you should be able to use some relatively simple math to determine where all the points would be placed on the perimeter of the inscribing circle.

-- KevinPerry

Alright, since I actually found some code tell how to do it (to some extent) but can't seem to implement a simple rect:
    
NSRect polygonRect;
            
            polygonRect.origin.x = center.x - size;
            polygonRect.origin.y = center.y - size;
            
            float width = [polygonRect bounds].size.width;
            float height = [polygonRect bounds].size.height;


----

The following code will make an NSBezierPath of a radius 1 regular polygon centered around the origin.  You'll need to use an NSAffineTransform to scale, rotate and move it to where you want, but it seems to work fine. -- Bo
    
<code>
@interface NSBezierPath (RegularPolygon)
+ (NSBezierPath*)bezierPathForRegularPolygon:(int)numberOfSides;
@end

@implementation NSBezierPath (RegularPolygon)
+ (NSBezierPath*)bezierPathForRegularPolygon:(int)numberOfSides
{
	// first create an empty path
	NSBezierPath* path = [NSBezierPath bezierPath];
	int i;

	// now move to the starting point (1, 0)
	[path moveToPoint:NSMakePoint(1.0, 0.0)];

	// now go around the circle adding lines to each of the other points
	for (i = 1; i < numberOfSides; i++) {
		// figure out the angle of the current point
		double angle = 2 * M_PI * i  / numberOfSides;

		// use the angle to figure out the x & y coordinates
		float x = cos(angle);
		float y = sin(angle);

		// add the point to the path
		[path lineToPoint:NSMakePoint(x, y)];
	}

	// finally, close the path and return it
	[path closePath];
	return path;
}
@end

// a sample NSView draw function to show how to use it.
- (void)drawRect:(NSRect)inRect
{
	// make 8 sided regular polygon
	NSBezierPath* path = [NSBezierPath bezierPathForRegularPolygon:8];
	// create a default transform
	NSAffineTransform* transform = [NSAffineTransform transform];

	// center it in the view
	[transform translateXBy:NSMidX([self bounds]) yBy:NSMidY([self bounds])];
	// make it's radius 1/4 the view's width
	[transform scaleBy:(NSWidth([self bounds]) / 4.0)];

	// get a new path with the transform applied
	path = [transform transformBezierPath:path];
	// draw it
	[path stroke];
}

</code>

----
All hail the great Bo. -- JohnDevor
----
Alright, your code works great, but could you or somebody else explain the "M_PI"? In the:     double angle = 2 * M_PI * i  / numberOfSides;
-- JohnDevor
----
M_PI is the constant pi (3.14159...) from math.h.

Digression: Basically, the angle of a full circle measured in radians is 2*pi (as opposed to 360 degrees).  So, on a pentagon for example, this function first returns 2*pi * 1/5 (equivalent to 72�), then 2*pi * 2/5 (144�), then 2*pi * 3/5 (216�) and then 2*pi * 4/5 (288�), producing 5 equally sized 'slices' of the circle.  

Hope that clears things up.  -- Bo
----
Yes, it does. I think I might actually get a firm grasp on trig when I get to it. At the moment, however, I'm stuck in geometry: a course that is tedious and  slow... -- JohnDevor

