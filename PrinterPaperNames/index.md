---
layout: page
title: PrinterPaperNames
---


I am trying to write my own print panel.i have got some problem accessing the paper types. I have noticed default page layout sheet displays all kind of papers ( Legal, letter etc) even though there is no printer installed. it can even print pdf, preview etc. But when i try to do this i could not create NSPrinter when there is no printer installed. can any one tell me how to get the all paper types? How to print pdf using NSPrinter.?

----
For what purpose are you writing your own print panel? The reason I ask this is beacuse Cocoa's default print panel implementation is one of the things you get for free from Cocoa, with no need to worry about how things print, just to give the printer data. The print panel is also extendable with a custom accessory view, if it fits your needs. I am just suggesting this as it would remove the need to implement printing yourself. --LoganCollins
----
Have you seen Aperture application? it comes with a different print options.. i HAVE to develop print panel some thing like that. it should have page layout and print optios in single window. apples print panel is good, but it's UI doesnt suit my requirement.

