---
layout: page
title: OddNSStringBugSourceLinked
---



http://www.staronesw.com/CardMachine.zip

Run the program and then do the following:


*Load Image - choose an image
*Click the Main Image dropdown and choose Object 1
*Click the Image dropdown and choose Horizontal text
*Click the Invisable dropdown and choose Above
*Click the Object 1 dropdown and choose Main Image
*Load another image
*Move it with X and Y sliders. 


The program will mess up, and usually quit.

I get this:    *** -[NSCFString replaceOccurrencesOfString:withString:options:range:]: nil argument

The app won't quit if the two occurances of:

[tstrin drawAtPoint:NSMakePoint(xv[temp],yv[temp]) withAttributes:attrs]; 

in Itemcontroller.m

Any idea why?

*nil argument means you're passing nil to a function that won't accept it-- use the debugger to find out which variable is nil, and either fix what's causing it, or put in some error checking to handle it if it is.*

