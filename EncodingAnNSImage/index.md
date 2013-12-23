---
layout: page
title: EncodingAnNSImage
---

Is it possible to encode an NSImage in a file with other data using NSKeyedArchiver?
----
Yes.
Grab NSData from any or all of the NSImage's image reps and archive the data.
The following link may be helpful to understanding what NSImage is:
http://www.cocoabuilder.com/archive/message/cocoa/2005/6/23/139845
See NSImage - (NSData *)TIFFRepresentation or - (NSImageRep *)bestRepresentationForDevice:(NSDictionary *)deviceDescription
and then encode the image rep.
----
Is the image vector or bitmap ?

----

OK, thanks!

It's a bitmap image.

