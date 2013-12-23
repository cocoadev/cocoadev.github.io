---
layout: page
title: HowToCreateNSPDFImageRep
---



Does anyone know how to create a NSPDFImageRep from a series of drawing commands (such as [bezier stroke] etc.)?

----

If your drawing commands are in an NSView then you can simply call:

    
[self writePDFInsideRect:[self bounds] toPasteboard:pb];


which will, in turn, call drawRect: on your NSView and translate the drawing commands into a PDF data stream. It's easy to get the NSPDFImageRep from the pasteboard. -ChrisMeyer

----

Using the same concept issuing drawing commands in a view, it should also be possible to call:

NSPDFImageRep *myPDFRep = [NSPDFImageRep imageRepWithData:[aView dataWithPDFInsideRect:aRect]];

or

NSPDFImageRep *myPDFRep = [[NSPDFImageRep alloc] initWithData:[aView dataWithPDFInsideRect:aRect]];

and get the same result. In the first example, don't forget to retain the instance if you want to keep it around. -BrockBrandenberg

