---
layout: page
title: CFURLGetFSRef
---

    
 Boolean CFURLGetFSRef (
    CFURLRef url,
    FSRef *fsRef
 );


Retrieves the FSRef associated with a given CFURLRef; returns false on failure, true on success.

I presume the CFURL must be a file:// URL, or this function won't do any good. *The documentation does seem to imply that*.

NSURL object pointers are TollFreeBridged with the CFURLRef type -- you can cast them freely between both types and use them happily and nicely with either Cocoa or CoreFoundation api calls. See NSURL for more information on the NSURL type.

Apple Documentation:
http://developer.apple.com/documentation/CoreFoundation/Reference/CFURLRef/Reference/reference.html

