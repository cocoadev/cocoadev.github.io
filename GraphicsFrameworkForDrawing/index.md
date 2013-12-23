---
layout: page
title: GraphicsFrameworkForDrawing
---

I want to draw some thing like what quick model draws. So i want to know wether there is any framework for drawing these kind of rectagles, arraows etc. I know we can achieve this using bezierpath. but it would be easy if any opensource drawing framework exist. any one know ablout this?

----

----

You won't find anything easier to use than NSBezierPath. It's not the fastest thing on earth, but you get a lot of stuff "for free".

----

Agreed. Drawing a solid (filled) rectangle can be done with one line:

    
[[NSBezierPath bezierPathWithRect:NSMakeRect(10, 10, 100, 50)] fill];


Other shapes (like a triangle) are slightly more complicated in that you must define the points (then use -moveToPoint: and -lineToPoint: to draw trace it), but if you find anything that reduces basic drawing to an even easier approach, you've found the Holy Grail. 
----
There is also 
    
NSRectFill(NSMakeRect(10, 10, 100, 50));


However, generic drawing frameworks for USER EDITABLE graphics certainly do exist.  If the original question may really be about user editable graphics.  There are also high level programatic interfaces e.g. GNOME Canvas.

There are also frameworks like Graphviz that meet specialized needs for directed graphs, diagram layout with minimized line crossing, etc.  

----

There's also GCDrawKit, which I'm currently developing with others, to be an open source Cocoa-based general purpose graphics editing framework.Feel free to have a look and perhaps get involved. It's not quite ready yet, but we are making good progress. --GrahamCox
----
I need to edit it programatically. GCDrawKit is not available yet. I need to develop just similar to quick model of xcode. drawing arrows stuff i found bit diff. incase there is no framework available i have to go for beziers.

