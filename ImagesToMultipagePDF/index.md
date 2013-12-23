---
layout: page
title: ImagesToMultipagePDF
---



Is it possible to use a number of images to create a multipage PDF file? I've seen some things around the net using text from an NSTextView, where adding images shouldn't be a problem, but it's not really a viable solution. Does anyone know how this could be accomplished?

**To create a PDF with one image per page, subclass NSImageView. Implement -rectForPage:, calling [self setImage:] with a different image depending on the page number. Also implement -knowsPageRange: and -setPrintableRect:. In your controller class, use NSPrintInfo and NSPrintOperation -printOperationWIthView:PrintInfo: to print the view to a PDF (you can supress the print dialog). There's some work to do setting margins, -setPrintableRect, etc. I learned how to print this way from the Anguish Cocoa Programming book (old but mostly still applicable). --Paul Collins**

