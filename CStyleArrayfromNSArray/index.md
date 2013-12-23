---
layout: page
title: CStyleArrayfromNSArray
---

I am working with an API that takes an array of char*.  I have an NSArray of NSString which I would like to convert.  Is there an easy way to do this.  I have done this in c++ by just passing in the address of the first element in the vector.  I can't seem to find a quick workaround for this in Obj-C.  Thanks.

----

You have to build it manually. Something like this code should do it:

    
const char **cStringArray = calloc([stringArray count], sizeof(*cStringArray));
int i;
for(i = 0; i < [stringArray count]; i++)
   cStringArray[i] = stringArray objectAtIndex:i] UTF8String];

// note, you must free() cStringArray when finished, the strings themselves will be effectively "autoreleased"


----
That may or may not be the best way to convert an [[NSArray of NSStrings to an array of char*s, but there's definitely a better way to get an array of NSStrings out of it.

    
NSString* stringsstringArray count;
[stringArray getObjects:strings];


----
The trouble is that when it comes to the original query, you haven't solved much. You still have to create the array of C strings, then iterate through the array of NSStrings and convert them one by one.

----

You're also relying on a GCC extension to C (the array length is dynamic, but the array is not dynamically allocated.) This is provided by GCC for convenience, but can cause stack exhaustion if you don't check the length of the NSArray before creating the C array.

----
This extension is also part of C99 so it's not technically compiler-specific, although it obviously may cause problems if you try to move to a compiler that doesn't yet support C99. But since it's in the standard there is at least no danger of gcc dropping support for it, and any new compiler should support it too.

