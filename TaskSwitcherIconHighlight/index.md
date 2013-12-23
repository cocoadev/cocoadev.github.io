---
layout: page
title: TaskSwitcherIconHighlight
---



I've looked everwhere for info but I guess i'm not sure what i'm looking for - i'm a newbie numbskull.

----

What exactly do you mean by a "highlight icon"?

*Are you trying to make a bezel? Or are you just trying to get an app's icon?*

----

I'm trying to reproduce the functionality found in the task switcher (Apple-T) where you see an image of the open application icons and then they highlight when they are selected. I have seen this type of thing in many apps an wondered if there is a known way of doing it.

----

Sure. Firstly, you make a window with the borderless window mask. Add a subview (or make its content view) a view that draws a black rounded rect with .66 opacity. Then use General/NSWorkspace's launchedApplications to get details on the launched applications and to get their icons. Draw more rounded rects around the selected one and then use General/NSTextFieldCell (or General/NSAttributedString) to draw the titles. -- General/AndyMatuschak

----

Great thank you - that gives me some direction now for learning this stuff

----

Keep in mind that General/NSBezierPath cannot draw rounded rects by default... there are some categories that will, though, and I believe one is on this site.

It certainly *can*, a rounded rect is just four arcs connected by four straight lines, which General/NSBezierPath can handle with extreme ease. There's just no built-in method for getting or drawing a rounded rect all in one shot.

*(see General/RoundedRectangles) -- General/AndyMatuschak*

*He means there is no convenience method on General/NSBezierPath for doing so.*
