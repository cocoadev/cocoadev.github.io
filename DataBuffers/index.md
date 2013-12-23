---
layout: page
title: DataBuffers
---



Hi,

I am a newbie to Cocoa Programming and I have a question concerning data buffers.

The obvious one is of course, what is a data buffer? Many of the APIs have function calls which have arguments that are pointers to a buffer of data. Can someone plese tell me what a buffer of data really means? How do you create one?

Does opening a file, for example an image file, or a sound file, mean that the program creates a buffer of data of the file that was opened?

Any clarifications would be really helfpul in getting me to understand how Cocoa programming works.

Thanks.

Shaun.

----

Often, it's an     unsigned char array, or another type of array (such as short, etc). It depends on the size of the data.

Buffers, when used in Cocoa, are generally normal C data types. If you don't know C, then you should go read up on C because you won't really know how to work with them, since you need to know how C pointers work in order to access the data inside a buffer.

For example, if you have an NSData object, you can get the raw data inside it via the     bytes method. This method will return     const void *, which you should then typecast to another C data type. If you wanted to access every 2 bytes inside this data, you could do something like this:
    
NSData *myData = [NSData dataWithContentsOfFile:someFile];
const unsigned short *rawData = (unsigned short)[myData bytes];

This will take every 2 bytes from myData and put it into an unsigned short array. The length of rawData would be [myData length]/2 since you're getting every 2 bytes (    length returns the number of bytes). Since this is a normal C pointer, you can use the [] syntax to get data at a certain index.

Hope this helps!

----

Hey, thanks a lot. That definitely was illuminating. 

I do have experience with C so I was able to follow what you said.

Thanks again.

Shaun.

