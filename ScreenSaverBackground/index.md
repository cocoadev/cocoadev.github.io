---
layout: page
title: ScreenSaverBackground
---

I am having some trouble setting the background picture of my screensaver. I have an image (.pdf) in a known path that I want to set to the background when my screensaver starts. Also, I am guessing it is quite possible to draw on top of that with bezier paths. Correct? Thanks for your help. (XCode, 10.3).  -Zac

----

If you are to redraw the entire background picture every frame, you will end with an ugly framerate, unless you use something faster like openGL. So if you want to animate, you'll probably need to redraw the screen partially, every frame. If you don't want to animate, it's not a problem. You just draw everything you want on top of your background and the screensaver engine will do the rest. You usually execute the preliminary drawings in the drawRect: method, which is called only once, when the screensaver starts...
    
NSImage *myImage = [NSImage initWithContentsOfFile:@"myPath"];
[myImage compositeToPoint:NSMakePoint(x,y) operation:NSCompositeCopy];    // x and y == 0, I guess...


-- Trax

----

**Display Image in center of ScreenSaver**

    
- (void)animateOneFrame
{
    NSRect bounds = [self bounds];
    NSImage *dilbert = [[NSImage alloc] initWithContentsOfFile:@"/Users/poodimoos/Desktop/2209810.gif"];

    NSSize bsize = bounds.size;
    float bwidth = bsize.width;
    float bheight = bsize.height;
    NSSize isize = [dilbert size];
    float iwidth = isize.width;
    float iheight = isize.height;
    float owidth = (bwidth - iwidth)/2;
    float oheight = (bheight - iheight)/2;
    NSPoint originPoint;
    originPoint.x = owidth;
    originPoint.y = oheight;

    [dilbert dissolveToPoint:originPoint fraction:(1.0)];
    [dilbert release];
    return;
}

