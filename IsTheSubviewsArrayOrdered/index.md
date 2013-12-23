---
layout: page
title: IsTheSubviewsArrayOrdered
---



Quick question, I'm writing a program that, currently, depends on the order of the array returned by NSView's subviews method, and the order of which subviews are added to the superview (I enumerate through the array to reposition view elements after a resize). Now, I haven't had any problems yet, but does anyone know if this might cause trouble later on? I just need to know that Cocoa won't have some strange behavior that only occurs in rare conditions. :)

----

To quote TheFineManual:

*
- (NSArray *)subviews

Return the receiver�s immediate subviews. The order of the subviews may be considered as being back-to-front, but this does not imply invalidation and drawing behavior. The order is based on the order of the receiver's subviews as specified in the nib file from which they were unarchived or the programmatic interface for modifying the receiver's subview list. This ordering is also the reverse of the order in which hit-testing is done.
*

----

thanks-- I suppose I should have double checked the docs before posting this. I know I must have read it before, but for some reason I was sure that wasn't mentioned when I was writing this code.

