---
layout: page
title: CFStreamClientContext
---

Defined as:

    
struct CFStreamClientContext {
   CFIndex version;
   void *info;
   void *(*retain)(void *info);
   void (*release)(void *info);
   CFStringRef (*copyDescription)(void *info);
} CFStreamClientContext;
 

In the "Working with Streams" section of the CFNetwork guide, the example uses custom function pointers as listed below:

    
CFStreamClientContext myContext = {0, myPtr, myRetain, myRelease, myCopyDesc};
 

It's much easier, and just as valid to use the built in CoreFoundation functions:

    
CFStreamClientContext myContext = {
    0,
    self,
    (void *(*)(void *info))CFRetain,
    (void (*)(void *info))CFRelease,
    (CFStringRef (*)(void *info))CFCopyDescription
};

