---
layout: page
title: SettingBorderStyleForPrinting
---

Hi.  I'm trying to print a window's contents.  Right now I'm calling window contentView] print:nil]; but this gives me a border that I do not want.  Is there any way to remove the border short of putting everything into a [[NSView?

----

Well, since the view you're asking to print has a border, the view's printing its border ... so probably not (unless you know how to get an inset rect of the view and manipulate that. What happens if the window's contents are too large for a page? You really shouldn't ignore all that and it only takes a little searching on this site to get some sample code for printing a view the right way (considering paper size, etc.).

