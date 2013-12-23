---
layout: page
title: CGRect
---

CGRect is the CoreGraphics equivalent of NSRect.

In 64 bit, NSRect is typedef'd to be the same as CGRect.  This means you can call functions taking CGRect with an NSRect and vice versa with no explicit conversion and without compiler warnings.  Unfortunately, this isn't the case in 32 bit.  NSRect predates CGRect, and changing the typedef broke apps that were depending on the value of @encode(NSRect) in method signatures.  

The upshot is that you need to convert NSRect to CGRect and vice versa if you target 32 bit Mac OS X.  

    
CGRect cgrect1 = NSRectToCGRect(nsrect1);
NSRect nsrect2 = NSRectFromCGRect(cgrect2);


These conversions are no-ops, they just shut up the compiler.

