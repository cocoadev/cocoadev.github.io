---
layout: page
title: NSImageComparison
---




Hello everyone.

Is there a way to easily compare two NSImage's? I have tried that via -(NSData*)TIFFRepresentation - then -(BOOL)isEqualToData:(NSData*)data2, but it doesn't work all the time. I need it for icon comparison in a NSTableView (using NSSortDescriptor)...

Thanks for any clues!

- Krystof

----
Comparing images like this is rarely a good idea. Your image came from somewhere, compare based on that. For example, if it's associated with a bundle ID, use the bundle ID to sort. If they come from files, use the file extension or type code. For web sites, use the URL, etc.

----

The problem is the program should be able to deal with custom icons, too. So, let's say in Finder, if you had an "Icon" column, you press it, and all files of the same icons would get together - sure, there is a way to send the isEqualTo: to the image, but this leaves out the problem when they don't equal - which we decide is "less"?

----
In Finder, both the icon and the sort order are based on the file type which is in turn based on the extension or type code.  There is no sort based on icon: there is only sort based on type.

----

That's why I said IF Finder HAD. I know it doesn't.

----
Basically, you want to define a partial ordering on the domain of NSImages.  Since one doesn't really exist, you'd be free to come up with your own metric.  And what do you if you need to compare a lossy-compressed and a lossless or uncompressed image?  They might be the same image to the eye, but not in terms of data.

----
I've done something similar before (though not in Cocoa) - firstly cache images so if they're identical (data) then the image pointers are the same, secondly I defined a metric which in my case was the average 'lightness' of the image (and store this with the image so you don't need to recalc)... BUT whatever you do, you really need to avoid recalculating the comparison metric all the time!

