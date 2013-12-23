---
layout: page
title: NSCompositingOperation
---



    NSCompositingOperation is an EnumeratedType defined by NSImage that describes how drawing is to be done.

The constants are described in terms of having source and destination images, each having an opaque and transparent region. The destination image after the operation is defined in terms of the source and destination before images as follows:
*     NSCompositeSourceOver: **Commonly used.** Draws the source atop the destination, with transparency. 
*     NSCompositeCopy: **Commonly used.** Draws completely replacing the destination. If the source has transparency, the destination also *becomes* transparent.
*     NSCompositeClear: For clearing out an area.
*     NSCompositeDestinationAtop
*     NSCompositeDestinationIn
*     NSCompositeDestinationOut
*     NSCompositeDestinationOver
*     NSCompositeHighlight
*     NSCompositePlusDarker
*     NSCompositePlusLighter
*     NSCompositeSourceAtop
*     NSCompositeSourceIn
*     NSCompositeSourceOut
*     NSCompositeXOR: Exclusive-OR of source and destination. Both must be black and white images. Quartz doesn't really support this mode anyway.


An example is on your HD at file:///Developer/Examples/AppKit/CompositeLab.

Many drawing functions let you specify the operation to use, including     -[NSImage compositeToPoint:fromRect:operation:fraction:],     -[NSImage drawInRect:fromRect:operation:fraction:], and     NSRectFillUsingOperation().

Prior to 10.4, functions that don't take an operation parameter will choose their own.     NSBezierPath uses     NSCompositeSourceOver, while     NSRectFill() uses     NSCompositeCopy. See FillRectVsNSRectFill.

On 10.4 and later, functions that don't take an operation parameter will usually use     NSGraphicsContext's, which can be changed with     -[NSGraphicsContext setCompositingOperation:]. The default is     NSCompositeSourceOver.     NSRectFill() *ignores*     -[NSGraphicsContext compositingOperation] and continues to use     NSCompositeCopy.

If you need to draw a     NSBezierPath with a specific     NSCompositingOperation on 10.3 or earlier, you can do so like this:
    
[NSGraphicsContext saveGraphicsState];
[bezierPath addClip];
NSRectFillUsingOperation([bezierPath bounds], operation);
[NSGraphicsContext restoreGraphicsState];


----
These are the classic Porter-Duff rules for digital image compositing upon which modern imaging is built. The original 1984 paper can found at http://www.keithp.com/~keithp/porterduff/.

