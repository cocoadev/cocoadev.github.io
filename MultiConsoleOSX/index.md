---
layout: page
title: MultiConsoleOSX
---

Does anyone know if it will ever be possible to have something like X-style forwarding for OS X (Aqua)?

Ever be possible?  Sure, anything is possible.  Near-term, probably not.  Rendering by Quartz is done by the process doing the drawing into a chunk of shared memory, and then the window server blasts those bits to the screen.  (Quartz Extreme employs the graphics card to do that work).  For things to do X-style stuff, either the whole bitmap will need to be shipped across the network (slow.  but maybe with the prevalance of gigabit networking in Macs these days...), or else the Quartz layer will need to be changed to not render locally, but accumulate the drawing commands (like when it generates a PDF, or for printing) and then ship those over the network.

That's too bad.  Even when pushing only the parts of the bitmap that have changed (i.e. RemoteDesktop) it's slow and ugly... 

Back in the Good Old Days, when there was Display Postcript, the application generated postscript code and shipped it off to the window server.  With that model they could just ship that same postscript off to a ifferent machine for rendering.

----

Wasn't Quartz supposed to be Display PDF, the descendant of Display Postscript? If it was, it would only to be to send the PDF-commands just as PS-commands to a different window server.

Quatz just uses the PDF imging model.  It doesn't actually generate or consume PDF under the hood, only when you explicitly ask for it via printing or dataWithPDFInsideRect:

