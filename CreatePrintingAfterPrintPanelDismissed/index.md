---
layout: page
title: CreatePrintingAfterPrintPanelDismissed
---

I'm trying to create my printing view after the user has dismissed the print dialogue. The reason for this is that I have an accessoryPanel in the print dialogue that allows the user to print only a specified type of data. But NSPrintOperation needs to be created before I can show the print panel. And in order to create NSPrintOperation I need to supply it with a View. But, to create the view I need to know which data to put in it which is only described in the print dialogue box.

So you see, I'm going in circles. Is there a way around my connundrum? Thanks.

----

I think the view is requested to draw only after the print panel has been dismissed. This quote from Apple's documentation seems to hint to that:

"You may also need to adjust your drawing based on an attribute in the print operation�s NSPrintInfo object. You can get the current print operation with the NSPrintOperation class method currentOperation and then get its NSPrintInfo object from the printInfo method."

Have you tried getting the results of the controls inside your print view's drawRect: method?

-- RyanBates

----
Thanks for the tip. You were correct. I basically changed my View's init method to make a blank small frame. When the printOp asked for the page range, I then populated my array of data and expanded the frame appropriately. Thanks again.

