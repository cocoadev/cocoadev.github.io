---
layout: page
title: PositionOnScreen
---



Just trying to see if the mouse is within my tableview when a particular method is called, here's my code it is within an NSView subclass, 

    

NSPoint widgetOrigin = [self frame].origin;
NSPoint widgetOriginInWindowCords = [self convertPoint:widgetOrigin toView:nil];
NSPoint windowOrigin = self window] frame].origin;
[[NSPoint widgetOriginOnScreen = NSMakePoint(windowOrigin.x + widgetOriginInWindowCords.x,
                                           windowOrigin.y + widgetOriginInWindowCords.y);


NSRect rectForSelfOnScreen = NSMakeRect(widgetOriginOnScreen.x, widgetOriginOnScreen.y, [self frame].size.width, [self frame].size.height);

NSLog(@"The rect on screen is %f %f %f %f ", widgetOriginOnScreen.x, widgetOriginOnScreen.y, [self frame].size.width, [self frame].size.height);

if (NSMouseInRect([NSEvent mouseLocation], rectForView, NO))
{
...do something...
}



I just need a second pair of eyes, this is about the 7th try i've had at this code, it started out just using convertRect: fromView:.

It just doesn't seem to tell me whether the pointer is indeed within the view (which is 'self' in the example above), but gives me strange results.

!

Thanks!
----
Try NSWindow's     convertBaseToScreen: method instead of all that     NSMakePoint(...) stuff. Also, while maybe your code *should* work, make sure you know if your view is flipped...if it is, your y-value would be completely off. --JediKnil

