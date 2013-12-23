---
layout: page
title: NSStatusItemImage
---


I made an icon with icon composer (/dev/tools), with all the sizes. But when i add it to my project. It works fine as the NSApplicationIcon, but as an icon for the NSStatusItem, it's too big! What do i need to do? 

*just use a tiff as the icon*

----

And just use that instead of the icon composer made one?

*yes, after resizing it to fit in the menu bar, obviously.*

----

Ok any idea of how big it should be ?
And there's no chance of having one-for-all ? :)

*You can use an icns, just reference it with imageNamed:.*

how do you get the proper size representation from that though? the size should be like 20x20.

*NSImage has a method "-setSize:", use that.*

**Just a note, icns supports 16x12, 16x16, 32x32, and 128x128, although you can scale the image dynamically to any size.**

----

so :
    
NSImage *myIcon = [NSImage imageNamed: @"NSApplicationIcon"];
[myIcon setSize: NSMakeSize(20,20)];
[myStatusItem setImage: myIcon];


should work?
Seems like the image disappears from the icon space.. 

----
Try this:
    
NSImage *myIcon = [[NSImage imageNamed: @"NSApplicationIcon"] copy];
[myIcon setScalesWhenResized:YES];
[myIcon setSize: NSMakeSize(20,20)];
[myStatusItem setImage: myIcon];
[myIcon release];

We make a copy because imageNamed: will return the same image every time, and we don't want to modify the original. The key fix is the call to     -setResizesWhenScaled:, which will actually scale the image rather than just changing the visible area when you call     -setSize: on the next line.

----
Ok, what you need to do is extract the 128x128 representation of your icon, and size it down to 20x20. Do it with photoshop or your favourite image editor, and save it as a separate file. You need to have a standalone 20x20 .png or .tiff (these are preferred formats) version of your icon. Add this to the project, and use [myStatusItem setImage:[NSImage imageNamed:@"statusItemIcon"]]; Don't forget the resolution of your 20x20 image has to be 72 and no more or no less or else the image will not display correctly in the status bar or in any windows etc. -- Goodluck! Louis.

