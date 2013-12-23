---
layout: page
title: PackedRGB
---

Im trying to output a gif, the colors are all messed up. I figured it was because I passed nil to the properties in the following:

    
 icon = [[[NSBitmapImageRep imageRepWithData:[newIcon TIFFRepresentation]] representationUsingType:NSGIFFileType properties:nil] retain];

Im trying to do this:
    
    icon = [[[NSBitmapImageRep imageRepWithData:[newIcon TIFFRepresentation]] representationUsingType:NSGIFFileType properties:
    [NSDictionary dictionaryWithObjectsAndKeys:
    ?????,NSImageRGBColorTable, NULL]] retain];

But I do not know what to put in to the ?????, documentation says:
----
*
The RGB color table. Used only for a GIF. It's stored as packed RGB. It's set when reading in and used when writing out.
*
----
But I can not figure out what that is, or how to get it.
----
A quick look in NSBitmapImageRep.h shows that the object for the NSImageRGBColorTable key should be a (as mentioned in the class's header file) "GIF input/output (packed RGB in NSData)." So it's some NSData that represents the GIF's RGB color table, but unfortunately, I haven't ever worked with this stuff, so I'm clueless to what that NSData should contain. I'll see what I can find for you.

-KevinPerry
----
Ok, I'm going to assume that the order that Photoshop uses for it's HTML color table is a good one for a typical GIF. The way Photoshop orders it is they start with FFFFFF, then go to FFFFCC, FFFF99, etc, subtracting 0x33 or 51 until it gets to FFFF00. From there it starts at FFCCFF, then FFCCCC, again subtracting 0x33 or 51. So basically, just create an algorithm to start at FFFFFF, subtracting 0x33 from the last byte until it's zero (adding these to your data buffer of course as each is formed), then subtract 0x33 from the middle byte while the last one wraps around, etc, like an odometer counts up, until you get to 000000, which is your 256th and last color.

Does that even make sense? Well, I hope it gives you an idea to get where you're trying to go.

-KevinPerry

----
I will look into that, kind of confused, but got the genral idea. I think the ??? are in their right spot, the objects are provided before the keys in a dictionaryWithObjectsAndKeys list and terminated with a null entry.
----
Oh whoops... sorry, I looked at that and thought I changed it when I realized that. I'll fix it now for anyone else who may read this.

