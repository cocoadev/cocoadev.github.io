---
layout: page
title: PrintingHeadersAndFooters
---

**Question: Does the Cocoa Printing architecture have support for headers and footers, in the sense of it shows on every page automatically like you'd expect in a word processor? I seem to recall reading in the AppKit docs that it does, but I can't find it again. If so, how do you implement it? Any help's appreciated. --LoganCollins** 

**Answer:** 
Apple provides the following documentation.
http://developer.apple.com/documentation/Cocoa/Conceptual/Printing/index.html

Printing with Cocoa is very easy (even automatic) in most cases.
When printing headers and footers and indeed any other marks such as crop marks, several decisions need to be made.
First, are the headers and footers part of the view to be printed and therefore visible on the screen, or are the headers and footers something that only shows up on printed pages ?

If the headers and footers are always drawn by the view (WYSIWYG) then nothing special is needed.  You presumably already know the page boundaries in order to draw the headers and footers on screen.  Just implement -knowsPageRange:, -rectForPage:, and -calculatePrintHeight as described in Apple's "Providing a Custom Pagination Scheme" documentation topic.

If the headers and footers are something that only shows up when printing, there are a couple of options:

A) Implement -drawRect: to draw the headers and footers only when printing and use custom pagination using -rectForPage: that includes the headers and footers.
The following code is taken from Apple's documentation:
     
- (void)drawRect:(NSRect)r {
     if ( [NSGraphicsContext currentContextDrawingToScreen] ) {
         // Draw screen-only elements here
     } else {
        // Draw printer-only elements here
     }
     // Draw common elements here
 }
  

B) Draw page borders separate from the body of the page.  From Apple's documentation:

* 
As part of the custom pagination, you can also add extra features to the page, such as crop marks, date/time strings, or page numbers. This is done, for each page, with drawPageBorderWithSize:. 

You must override drawPageBorderWithSize: to make it functional, as its default implementation prints nothing to the page. In this method, first save the view�s existing body frame�it will need to be restored at the end of the method. Once the old frame is saved, resize the body frame to a rect with origin (0, 0) and a size equal to the incoming borderSize parameter. This new frame now encompasses the margins instead of hiding them.

Once the frame is expanded, you can add your custom border elements to all four margin areas (top, bottom, left, and right). Drawing is typically done with drawAtPoint:. Any set of drawing calls must be preceded by lockFocus: and followed by unlockFocus:, otherwise drawPageBorderWithSize: will not draw anything to the page for those calls. Use the paper and margin dimensions from the print info object to constrain the printable area and prevent drawPageBorderWithSize: from printing within the body text frame. If you wish to print within the body text frame�to print a watermark, for example�do so by printing directly in the newly enlarged frame and ignoring the margin constraints.

Reset the frame to the body text area before exiting the method; this assures the next page of content will print only within the paginated portion of the view. 
*

