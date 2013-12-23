---
layout: page
title: HowToScaleViewWhenPrinting
---

I have an NSView subclass (which contains subviews as well) that I want to display on the screen with one size and in a printing context cause it and it's subviews to act as if the frame had been set to the size of the page.  I have tried many things to achieve this affect but am not making much headway... here's a brief summary of my failed attempts:

-divide drawRect into two methods, one for screen one for print detecting with [NSGraphicsContext currentContextDrawingToScreen] and adjusting rect accordingly... some variants of this technique scaled the main view but not the subviews, some were clipped, some did both... all failures

- use the rectForPage:/adjustPageWidth-Height methods to create a larger rect to be sent to the drawRect... fails, scales image up but shows only a slice; the size of the view in the window.

-use the knowsPageRange method to setFrame on the view up to the size of the page... fails, prints properly but distorts the screen view

-override the print: method setting frame size, calling [super print:sender] and the resetting frame... fails, prints nothing

-use AspectCocoa to add an aspect on print: for the containing view with before advice to set the frame to paper size and after advice to reset the frame to the old size for the screen... fails, prints at the small window size

-have a hidden view that contains a page sized NSImageView, when drawing the view update the image view with something like [printView setImage:[[NSImage alloc] initWithData:[self dataWithPDFInsideRect:rect]]]... fails, prints nothing, oh, and the window is ALL white (including the title bar...) something very strange is going on...

-set the view to the size of the page and put inside a scroll view... fails, prints properly but views display improperly on the screen as they are scrolled... this could be fixed with some work, but I'd rather not have a page sized view that I have to scroll around on my screen anyways... 


there must be a way to do this... many things print different representations than they show on the screen.  I don't even want to adjust the drawRect: ... I just want to drawPageSizedRect: for printing

----

Investigating NSAffineTransform to be applied in drawRect if it's printing.... failed... scales containing view but not subviews... print is clipped to rect the size of on screen view.

----

tried playing with print info in the drawRect method to set print scaling to a factor larger than the page and then set vertical/horizontal pagination to NSFitPagination... failed, no apparent effect

----

Victory!!!  it involves a couple steps

0) implement custom drawRect: method in NSView subclass

1) add a method for (void)knowsPageRange:(NSRangePointer)rangeRef

2) add a member variable to store the screen frame size in

3) add a method (NSRect)rectForPage:(int)pageNumber which does

	a) decide the size on the page you want to print

	b) save the screen frame in the member from part (2)

	c) setNeedsDisplay:NO for self and all subviews

	d) [self setFrame:pageRect]

	e) return pageRect

4) add a method for (void)endDocument (or endPage...) that

	a) calls the super classes implementation

	b) [self setFrame:screenRect]

----
Is there an example of this somewhere?

