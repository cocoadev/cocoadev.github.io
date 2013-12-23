---
layout: page
title: GenericDataExplorerWindow
---

I know this site primarily deals with coding issues but my question deals with program design. Please forgive if this is out of line, but I'm at wit's end.

I have a project that initially deals with 3 primary data records (eg A, B & C). I plan on using CoreData to store the information. I want to create a generic window that is initialized with settings that configure its title, the records it manages and most importantly, the "view" it should display to edit the data.

The window would be iTunes-like: an outline view on the left that would list the "names" of the object (eg, <lastname, firstname>) and the right-hand side of the window (what I call the detail pane) would be a CustomView (I think) that would be set to a view loaded at run-time initialization from a specific NIB file. 

Is this a viable design? I'm new to Cocoa programming and none of the books I've found (which do not even cover 10.3, let alone 10.4) have ever shown how to do this.

I have a working "mainmenu.nib" application. I've created a second NIB with a descendent of NSBox in it that I've created the detail form on. What I cannot figure out is how from a window's init code, to load the NSBox descendent and set it as the content of the CustomView (if that's even the right way to do it). Some examples I've found here mention setContentView and I wonder if that's the route to take.

I'd like to basicaly write one window that is generic enough to handle the UI portion of the work--just once, rather than writing it three times.

Lastly, could someone please clear up something for me: Controllers. I have one in the mainmenu.nib that so far does what one would expect. But some of the texts talk about a "custom" controller for windows (or objects) in separate NIBs if applicable to a given situation. At first glance, it seems that each window that is unique should be in its own nib and have its own controller, but I've not yet been able to really get that to work (the code never fires). Yes, I've set the File's Owner to the same class as the mainmenu.nib's controller, but isn't that defeating the purpose of a unique controller? How could such a design be properly implemented (ie, the connections between nibs made, if they even need to be???)

Thanks in advance.

