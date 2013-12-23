---
layout: page
title: NSImageAddressErrorOnDeallocate
---

OS X 10.3.1 in a ObjC++ (.mm) code file

I have a problem with NSImage and corrupt images.  I call [image isValad] and it returns YES.  But I get a log message in the debugger:

Corrupt JPEG data: 962 extraneous bytes before marker 0xd9

So obviously NSImage knows that the image is _not_ valid.

Is there a way to get this message?  If so I can skip rendering the image.

The real problem comes when NSImage is released:

Program received signal:  "EXC_BAD_ACCESS".

The call tree is from the debugger (last call last):
-[NSImage dealloc]
-[NsImage _freeRepresentation:]
-[NSImage cachedImageRep dealloc]
_NXFreeCache
objc_msgSend_stret

And my program crashes.  Basically this is unacceptable but I am at a loss to figure out how to avoid it.
I check the header with custom code to weed out obviously bad images but I can't catch all of them.
Can I catch signals?

