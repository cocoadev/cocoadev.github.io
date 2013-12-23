---
layout: page
title: NSViewToPDFWithoutDisplay
---

So anyway, I wanted to take the logic from my layout application and make it have a web front-end that would output pdfs that the desktop version would.  In case anyone else is looking for this tidbit I thought I'd share it.
--
After setting up the NSView to the proper state programmatically (while leaving it hidden by the way... or not even on a window...)

    
NSPrintOperation* myPO = [NSPrintOperation PDFOperationWithView:layoutView insideRect:kA4Rect toPath:outputPath printInfo:defaultInfo];
[myPO runOperation];


Note that this runs in the current run loop and will block until the printing completes.  You'll of course have to decide the sizing of your rect, where you want it output, and setup any other print options in the printInfo variable.

----

Cool, thanks for this tip

