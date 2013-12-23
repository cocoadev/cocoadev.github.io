---
layout: page
title: HowToOpen
---

HowToOpenAFileUsingCStdLib deals with the use of standard C function fopen()

To clarify, this page will tell you how to query the user for a file using the Open panel.  If you are more interested in archiving your own files and retrieving them, check out HowToSave, which explains how to do that.

If instead, you want to learn how to open an Open panel, read on faithful coder.  I did not find anything about the NSOpenPanel in any of the major tutorial books for Cocoa, but I figured it out!  It is very easy.  Here is a very simple way to use the NSOpenPanel to get the name of a file.

    
NSOpenPanel *op;                         // This declares that op will point to an NSOpenPanel
op = [[NSOpenPanel alloc] init];         // This creates and initializes the NSOpenPanel object
                                         // and assigns it to the pointer op
[op runModal];                           // This makes the open panel visible and usable for
                                         // the user and will store the path to the file
                                         // somewhere in op


You can access the path to the file by using the method..
    
NSString pathOfFile = [op filename];


The method above obviously returns an NSString which can be used in statements where you need the filename like...
    
NSImage *anImg=[[NSImage alloc] initWithContentsOfFile: [op filename]]; 


Now, does anyone want to write a description for how you use the NSString returned from the "filename" method to open a file?

Back to HowToProgramInOSX

