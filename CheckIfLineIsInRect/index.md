---
layout: page
title: CheckIfLineIsInRect
---



I've having some problem to detect if mouse is over a line between to NSPoint?

If the line is vertical and horizontal its easy.

But i cant figure out a way to to it when the points are way from each other

tnx

----

In theory, it's pretty easy. Calculate the slope of the line that connects the two points, and also calculate the slope of the line that connects the mouse position and one of the points. (Slope, btw, is the change in the y-coordinates divided by the change in the x-coordinates.) If they're the same, the three points (the two that you specify and the mouse position) are collinear. If you want to discern whether the mouse is actually between the two points and not just somewhere on the line that connects them, you only need to verify that either the mouse's x-coordinate is between the x-coordinates of the two point, or that the mouse's y-coordinate is between the y-coordinates of the two points.

In practice it's a bit more complicated because you probably want to allow points that aren't exactly on the line to be counted as though they are, so that the user only has to get close (assuming you're trying to do something like select the line). So rather than do the theoretical thing above, you really just want to calculate the distance between the mouse position and the line between the two points. If the distance is less than the allowed error, consider that the mouse is "on" the line. There are lots of web pages that describe methods for calculating the distance between a point and a line, so I won't bother repeating that here. One such page is: http://mathforum.org/library/drmath/view/63398.html
-CS

----

The above method may be tricky if you consider a finite length line. Cocoa does it much more easily : You make a NSBezierPath with a rectangle that encloses your line an then you check if the mouse is inside with pathContaintsPoint. This method works also with curved lines with an arbitrary shape.

    

float dx = p0.y - p1.y;
float dy = p1.x - p0.x;
float d = sqrt(dx*dx+dy*dy);
dx = 5*dx/d;
dy = 5*dy/d;

// (dx;dy) is a 5 points length normal vector to your line.
		
NSBezierPath *path = [NSBezierPath bezierPath];
[path moveToPoint:NSMakePoint(p0.x-dx, p0.y-dy)];
[path lineToPoint:NSMakePoint(p1.x-dx, p1.y-dy)]; // can be any curved line
[path lineToPoint:NSMakePoint(p1.x+dx, p1.y+dy)];
[path lineToPoint:NSMakePoint(p0.x+dx, p0.y+dy)]; // must be the inverse of the previous one
[path closePath];
		
if([path containsPoint:aPoint])
		{
			/* do something */;
		}



----

Tnx alot

