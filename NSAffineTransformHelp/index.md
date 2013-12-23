---
layout: page
title: NSAffineTransformHelp
---



Hi!

I'm having som trouble rotating a NSBezierPath with NSAffineTransform.

    

NSRect r = SomeRect();
NSBezierPath *path = [NSBezierPath bezierPath];
NSAffineTransform *rot = [NSAffineTransform transform];

[rot translateXBy:0.5*r.size.width yBy:0.5*r.size.height];
[rot rotateByDegrees:45];
[rot translateXBy:-0.5*r.size.width yBy:-0.5*r.size.height];

[path moveToPoint:r.origin];
[path lineToPoint:NSMakePoint(r.origin.x+r.size.width, r.origin.y)];
[path lineToPoint:NSMakePoint(r.origin.x+r.size.width, r.origin.y+r.size.height)];
[path lineToPoint:NSMakePoint(r.origin.x, r.origin.y+r.size.height)];
[path closePath];
[path transformUsingAffineTransform:rot];



When I use this code the NSBezierPath draw way from where i told it to, where is the problem?

tnx :)

----

Made it work:

    
NSRect r = NSMakeRect(0, 0, width, height);
						
NSBezierPath *path = [NSBezierPath bezierPath];
NSAffineTransform *rot = [NSAffineTransform transform];
NSAffineTransform *pos = [NSAffineTransform transform];
NSAffineTransform *main = [NSAffineTransform transform];
						
[rot translateXBy:0.5*r.size.width yBy:0.5*r.size.height];
[rot rotateByDegrees:a];
[rot translateXBy:-0.5*r.size.width yBy:-0.5*r.size.height];

[pos translateXBy:pos.x yBy:pos.y];

[main appendTransform:rot];
[main appendTransform:pos];

[path moveToPoint:NSMakePoint(0, 0)];
[path lineToPoint:NSMakePoint(r.size.width, 0)];
[path lineToPoint:NSMakePoint(r.size.width, r.size.height)];
[path lineToPoint:NSMakePoint(0, r.size.height)];
[path closePath];
[path transformUsingAffineTransform:main];


----

Ok, the above did almost work.

I'll just need to ask some questions:

I have a point NSPoint(100, 150);
and a size NSSize(10, 10) which contains half values

Then i draw bezierpath with the point as center
    
[path moveToPoint:NSMakePoint(point.x-size.width, point.y-size.height)];
[path lineToPoint:NSMakePoint(point.x+size.width, point.y-size.height)];
[path lineToPoint:NSMakePoint(point.x+size.width, point.y+size.height)];
[path lineToPoint:NSMakePoint(point.x-size.width, point.y+size.height)];
[path closePath];

Now I want to rotate the rectangle 45 degrees with the point as center.

How do I do that? just to make sure I got it right.

----

Better to make the path centre on the point {0,0}, then use the final position {100,150} as the x and y inputs to the translate transform. Rotations are always relative to the origin.

Basically, if you construct your path based on the origin (i.e. centred on a point {0,0}), you can simply rotate it around its centre using the rotate transform, THEN move it to its final position using a translate transform. The order generally is: rotate first, then scale, then translate.You need to create separate transforms for each and append them in this order - setting the three properties on a single transform doesn't work (or rather, it does what it should do, just not what you might expect it to do!). If the path isn't centred on the origin, then the rotation will rotate around whatever point is at {0,0} relative to your path. You can take advantage of this to provide a fourth property - an "offset" which allows you to set the centre of rotation to anything you like. This is applied first, then the rotate, etc... --GrahamCox

