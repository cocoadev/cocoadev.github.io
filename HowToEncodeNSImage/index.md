---
layout: page
title: HowToEncodeNSImage
---

I need help on how to encode NSImages so I can store them in a NSMutableArray.

----

Look at the docs for NSCoding and NSArchiver. Most simply, you could do 

    NSData *myData = [NSArchiver archivedDataWithRootObject: myImage] 

and add myData to your array. 

To unarchive the NSImage do     NSImage *myImage = [NSUnarchiver unarchiveObjectWithData: myData]

----

Um, pardon my density, but what precisely is keeping you from just adding the images to the array, i.e.     [theArray addObject:theImage]; ?  Arrays can hold NSImage objects (and indeed any ObjC object) just fine.  -- Bo

*Perhaps he wants to write the array out as a plist. NSImage is not a plist object, so the encoding is necessary for that.* 

Ah.  That makes sense.  I suppose, in that case, I should point out that you should call     [theImage setsDataRetained:YES]; before you archive it or it'll quite likely just encode the file name, and that     -TIFFRepresentation and     -TIFFRepresentationUsingCompression:factor: (and the     -initWithData: method to reconstitute) are also available to convert it to an NSData object.  -- Bo

