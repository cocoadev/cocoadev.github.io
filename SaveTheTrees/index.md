---
layout: page
title: SaveTheTrees
---

When I first started programming in Cocoa I actually printed
out the documentation for both AppKit and Foundation 
frameworks. You wouldn't believe how many pages that
was. The whole deal cost me a small fortune, but it was 
worth it to me at the time. The main reason I didn't like 
using the HTML based documentation was because I 
found myself scrolling too much. I could look up methods 
faster with printed documentation than with a mouse. 

Well, I finally broke down and decided to write a tool
that would place buttons throughout Cocoa's 
documentation to speed up the navigation process. This
tool will even examine the documentation for each class/protocol by 
searching each document for class methods and then inserting 
internal links to the methods at the top of the
page. 
 
It took me an entire day, but I figure the time was well 
spent because now I can lookup methods orders of 
magnitude faster than flipping through my printouts. 

I posted an example of what this tool does at the link below.
The only class you can browse is NSMenuItem so when you go to this page
the only active link you can click on is "NSMenuItem"

http://www.unifiedmodular.com/cocoa/QuickReference/AppKit/AppKitTOC.html

If there is enough interest in viewing Cocoa's documentation
this way and if people working with small screens dont like
how wide the tables of internal links are, I can modify this
tool to make the tables of internal links 2 or 3 columns instead
of 4. (I have to remember that not everybody has a 19" monitor
running at 1920x1440)

Anyone who wishes to try this tool out just follow the directions below:


*1. Make a directory named QuickReference (the name doesn't really matter)
*2. copy /System/Library/Frameworks/AppKit.framework/Versions/C/Resources/English.lproj/Documentation/Reference/ObjC_classic into QuickReference 
and rename the copied directory AppKit
*3. copy /System/Library/Frameworks/Foundation.framework/Versions/C/Resources/English.lproj/Documentation/Reference/ObjC_classic into QuickReference 
and rename the copied directory Foundation
*4. Launch ProjectBuilder and create a new "Foundation Tool" project called "QuickReferenceEdit"
*5. erase everything in main.m
*6. cut and paste the code below into main.m


----
code moved to CodeForSaveTheTrees, so I can read this...
----


*7. save and build the tool
*8. locate the binary for "QuickReferenceEdit" (hopefully you already know where PB puts it).
*9. copy the binary into the folder you named QuickReference (you only need to do this if you don't know your way around a shell environment)
*10. Launch Terminal app
*11. "cd" to "QuickReference" (i.e. cd /Users/bob/QuickReference)
*12. type "./QuickReferenceEdit"
*13. Inside the folders you renamed AppKit and Foundation you will find AppKitTOC.html and 
Foundation.html. Click on either of these two files and hopefully the filter did it's thing and you will be 
able to see quick buttons throughout the Cocoa API documentation. 


I know my code style is pretty sloppy and not very self documenting
so please be kind :-) Part of this is because I wanted to fit all the 
code into one file and I don't consider this a HowTo. 
Normally my code is more readable. 

This tool might have a large memory footprint (not a big deal if
you have 768M of RAM) You could add an inner NSAutoreleasePool 
to fix this:
    

+(void)editClassAtPath:(NSString *)file {
NSAutoreleasePool *innerPool=[[NSAutoreleasePool alloc] init];
.
.
.
.
.
.
.
.
[innerPool release];
}



--ZootBobbaLu



note: at the moment I have only tested this tool on Version C of the Cocoa Objective C API documentation so if anyone get this filter to work on other versions or the Cocoa Java API documentation please add to the list below.

successful versions filtered:

*Version C - Cocoa Objective C API Documentation

