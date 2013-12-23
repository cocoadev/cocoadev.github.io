---
layout: page
title: LKOverlayWindow
---

LKOverlayWindow is a class for the creation of a custom overlay windows which appear as though they are nested within a NSScrollView. 
(But they're actually not!! Haha take that cocoa!)

Design your overlay window in IB, drop this class in and set it to the custom class of the window. You may supply your own custom view for drawing the window background (this is the default), or you can use - (void)useDefaultBackground:(BOOL)backgroundBool to get the transparent black HUD style editors used in apps like iPhoto.

If used via IB, the window will init itself, however you must call - (void)attachToScrollViewOutlet or - (void)attachToScrollView:(NSScrollView *)scrollViewObject to complete the process and display the overlay window.
If allocated via code then you must call - (id)initWithHeight:(int)height then - (void)attachToScrollView:(NSScrollView *)scrollViewObject.

To detach the overlay window from the NSScrollView call - (void)detach. The window does not release itself from memory on detach.

----
Uh, links don't work:

"Forbidden
You don't have permission to access /LKOverlayWindow.zip on this server."

:\
----
Grrr! All fixed now. The links are now correct. Sorry folks! Just for the record, anyone know of some good hosting orgs?

