---
layout: page
title: NSBezierPath
---



http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSBezierPath_Class/index.html#//apple_ref/doc/uid/TP40004008

also see: http://developer.apple.com/documentation/Cocoa/Conceptual/DrawBasic/index.html

----

An NSBezierPath object allows you to create paths using PostScript-style commands. Paths consist of straight and curved line segments joined together. Paths can form recognizable shapes such as rectangles, ovals, arcs, and glyphs; they can also form complex polygons using either straight or curved line segments. A single path can be closed by connecting its two endpoints or it can be left open. 

----
[Topic]
----

Is it possible to somehow turn off antialiasing when using NSBezierPath objects?

----

Yep, just use [[NSGraphicsContext currentContext] setShouldAntialias:NO]
before drawing a path to turn antialiasing off and the same call with YES after drawing to turn it back on (if you want).

----

Apple might have spent a lot of time making their antialiasing algorithm, but they seem to have ignored aliased (not antialiased) drawing. A 1 pixel wide bezier path looks more like 2 pixels.

----

Well, I tried [[NSGraphicsContext currentContext] setShouldAntialias:NO], and even [NSBezierPath setLineWidth:0.0], no satisfying result. I still get rectangles with two pixels wide borders.

----

You need to move your rect over 0.5 pixels.  Use     NSOffsetRect(myRect, 0.5, 0.5) to move it over before drawing it.  You can use an NSAffineTransform to translate an NSBezierPath. -- Bo

----

It's probably worth mentioning that the reason for the 0.5 pixel offset is that Apple are viewing the screen as a grid, where the lines fall between the pixels (not on them), and when you draw a line, you specify the coordinates of the centre of the line. For example, the bottom-left pixel of the screen is a rectangle between the coordinates (0, 0) and (1, 1), with its centre at (0.5, 0.5).

If you specify an integer co-ordinate, you're trying to draw your line down between two pixels, so the best Quartz can do is to render half of it in each of them.

----

Here is a little method I ran into once for this. 

-- Johan Kool

    
- (NSBezierPath *) bobify
{
    NSPoint *pts = malloc(3*sizeof(NSPoint));
    NSBezierPathElement elem;
    int i,j;
    for (i=0;i<[self elementCount];i++) {
        elem=[self elementAtIndex:i associatedPoints:pts];
        switch (elem) {
            case NSMoveToBezierPathElement:
            case NSLineToBezierPathElement:
                //single point per element
                pts->x = roundf(pts->x)-0.5; //JK Changed +0.5 to -0.5 
                pts->y = roundf(pts->y)-0.5;  //JK Changed +0.5 to -0.5 
                [self setAssociatedPoints:pts atIndex:i];
            case NSCurveToBezierPathElement:
                //control point 1, control point 2, end point
                for(j=0;j<3;j++){
                    (pts+j)->x = roundf((pts+j)->x)-0.5; //JK Changed +0.5 to -0.5 
                    (pts+j)->y = roundf((pts+j)->y)-0.5; //JK Changed +0.5 to -0.5 
                }
                [self setAssociatedPoints:pts atIndex:i];
            default:
                break;
        }
    }
    free(pts);
    return self;
}


----
I'm working on a program that needs to trace the paths taken by objects on screen. Currently I have it set up so that each object has a bezier path, and calls [path lineToPoint:[self location]]; once per frame, and [path stroke]; once per frame. This gets VERY slow after a few minutes (several thousand segments being stroked every frame, plus some being added every frame). Can anyone think of a way of speeding this up? I would do something like draw the path into an NSImage so that I didn't need to redraw all the time, but the path can be arbitrarily long, so an image big enough to fit any path would be arbitrarily large.

*Could you not limit the image to screen resolution?*

I could, but I'd be losing some major functionality of my program. I think I'd prefer to limit trail length rather than working area (which is what I'll do if I can't think of something better)

----
Why is NSBezierPath so slow?  Why do plotting, clipping, stroking, and filling all take so long?  Am I doing something wrong?
----
It seems to me that you are drawing the WHOLE path for each object at each frame. Why don't you just draw the last line with something like:

    [NSBezierPath strokeLineFromPoint:[self previousLocation] toPoint:[self location]]

I may be missing something in what you are doing but at least, I tried ;-) --CharlesParnot

----

NSBezierPath / Quartz are slow with paths that self-intersect, since it has to handle the intersections specially (antialiasing, not making the overlaps draw darker with transparent, etc).  Sometimes you can get a speedup by have a bunch of little paths rather than a single big honkin' path

