---
layout: page
title: CoordinatesOfAnObjectOnScreen
---



Hi,

I have a window which shows a calendar and is designed to pop up when the user presses a button next to a textField where they are supposed to enter a date in my app.

I have written the calendar code fine, but I am having trouble finding out how to get the coordinates of a certain object on screen. I need these so that the calendar dialog can pop up next to the relevant textBox, and not in the middle of the screen someplace.

Two ways of doing this I can think of -

1. I know the on screen textBox object i want the calendar to appear for, so I could figure out the location on screen of this object, and make the calendar appear there.

2. I could just have the calendar appear under the mouse cursor (I think this may be better, but sometimes I change my mind)

My problem is, I don't know how to do either. The second one strikes me as being more simple than the first to achieve, but any help is much appreciated,

Thanks.

----

Look at NSView's convertRect:toView: and NSWindow's convertBaseToScreen: methods for 1.

For 2, you can just call [NSEvent mouseLocation].

----

Does anyone have a reliable way to get the screen coordinates of any widget, whether it's in a tab view or not?

----

the best way to get a position of a view is to use the following methods:

    

NSPoint widgetOriginInSuperviewCords = [widgetView frame].origin;
NSPoint widgetOriginInWindowCords = [self convertPoint:widgetOrigin toView:nil];
NSPoint windowOrigin = widgetView window] frame].origin;
[[NSPoint widgetOriginOnScreen = NSMakePoint(windowOrigin.x + widgetOriginInWindowCords.x,
                                           windowOrigin.y + widgetOriginInWindowCords.y);


the best way to get mouse location outside of an event:

    

NSPoint mouseLocationInWindow = widgetView window] mouseLocationOutsideOfEventStream];
[[NSPoint mouseLocationInWidgetView = [widgetView convertPoint:mouseLocationInWindow fromView:nil];



----

Thanks, that worked great, I just needed to make a couple of adjustments to get it to work from within a custom control that doesn't know what view it's on. I'll put it up here as a reference for anyone else who's trying to do the same thing.

    
NSPoint widgetOriginInWindowCords = self superview] convertPoint : [self frame].origin toView : nil];
[[NSPoint windowOrigin = self window] frame].origin;
[[NSPoint widgetOriginOnScreen = NSMakePoint(windowOrigin.x + widgetOriginInWindowCords.x,
                                           windowOrigin.y + widgetOriginInWindowCords.y);


----

You could simplify things somewhat more with the following code ("self" is the widget here)...
    
NSPoint widgetOriginOnScreen = self window] convertBaseToScreen: [self convertPoint: [[NSZeroPoint toView: nil]];


Or is there a reason for not using convertBaseToScreen? --PeterMaurer

*I'm not sure of the reasons, but it doesn't seem to work for a widget in a tab view.*

