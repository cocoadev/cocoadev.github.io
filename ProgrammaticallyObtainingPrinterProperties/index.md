---
layout: page
title: ProgrammaticallyObtainingPrinterProperties
---

I want to determine at run time whether the printer the user is printing to is a color printer or not. Is there any way to do this? I am using OS 10.1.5.

Based on reading the Apple documentation on printing in OS X I thought the following code would work:

    NSPrintInfo	*printInfo = [NSPrintInfo sharedPrintInfo];

    NSPrinter   	*printerInUse;

    BOOL       		isColorPrinter;


    printerInUse = [printInfo printer];

    isColorPrinter =  [printerInUse booleanForKey:@"ColorDevice" inTable:@"PPD"];


But [printInfo printer] returns nil, even though I am printing to a physical printer. And  [NSPrintInfo defaultPrinter] also always returns nil. I have seen this same complaint on other Cocoa developer forums but no answer has been given, at least as far as I can determine.

So, in spite of the OS X Cocoa documentation, I claim it is completely impossible to determine programmatically any information at all about a printer. Although I do not have Jaguar, I believe the same problem exists there, based on what I have seen on another forum.

PLEASE!!!! SOMEONE PROVE ME WRONG!

*In 10.2.6 [NSPrinter printerNames] will give you the names of all the printers available. And [NSPrinter printerWithName:printerName] will give you a printer. So it is possible to get some printer info. On the bright side, getting info out of the PPD works. On the downside,  my Epson printer's PPD file doesn't contain *ColorDevice:, so asking for that key doesn't work. I don't see any way to tell if the printer is color or not, which is probably why isColor was deprecated.*

