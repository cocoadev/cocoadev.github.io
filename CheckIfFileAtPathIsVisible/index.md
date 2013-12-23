---
layout: page
title: CheckIfFileAtPathIsVisible
---

Hello

I have a method that checks against 2 of the three kinds of file visibility (prefixed with a . and the name of the file being in a folder called .hidden) - but how do you check against the last type, the invisible flag embedded in the file.

Thanks�

----

Rather than showing you how to do that, here is the preferred way of determining invisibility by any known method:
    
NSURL *fileURL; // this must be valid
LSItemInfoRecord fileInfo;
OSStatus err;

err = LSCopyItemInfoForURL((CFURLRef)fileURL, kLSRequestBasicFlagsOnly, &fileInfo);
if (err == noErr && fileInfo.flags & kLSItemInfoIsInvisible) {
    // item is invisible
}

(you need to add ApplicationServices.framework to your project and include its header, too)

-- FinlayDobbie

----

Thanks !

