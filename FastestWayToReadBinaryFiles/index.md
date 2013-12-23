---
layout: page
title: FastestWayToReadBinaryFiles
---



I've been working on some code that reads a binary file (close to 3MB in size) and was wondering if anyone has any experiences with using straight C to read the file with fseek vs dumping the entire file into an NSData object and then reading it using NSData's     bytes method (which is what I do now). Do you think using straight C (functions such as fseek, fgetc, etc) would be faster?

----

They'll both be equally fast if you're reading the entire file in one big chunk. The bottleneck is going to be the hard drive, not the code.

You might consider using NSData's     +dataWithContentsOfMappedFile: method. This will memory-map the file rather than reading it all in, which will allow the OS to stream the data into memory as you access it. If your access patterns are good (i.e. you read the data from beginning to end in order), this can speed things up significantly because the hard drive can be reading chunk N+1 while you're processing chunk N. It also will make your app use less memory if it doesn't touch the entire file.

----

Remember that, almost all the time, accessing a file randomly is slower than reading it sequentially. 
There are two reasons why you might still want to use random access:

- The file is potentially very large (in which case, I beleive the memory mapped file as suggested would still be a great solution and 3Megs aren't all that big these days).

-  You only need a very small part of the file and you know exactly where it is in the file. 

For most applications, what you are doing is probably very adequate.

----

One other thing, if you're running in 32-bit mode (and as of this writing, all Cocoa code is in 32-bit mode, 64-bit apps can't use any Objective-C), then none of these techniques will work for extremely large files, greater than approximately 2-3GB in size, because you'll run out of memory address space. This doesn't sound like an issue for the original poster, but it's worth mentioning.

----

Note that Apple specifically recommends file mapping as a technique for getting around 32-bit limitations: you just map the part of the file you're writing/reading (and unmap it immediately afterwards). The memory manager of the OS, which is 64-bit, heavily caches the file, so it won't be actually written to disk (unless there's not enought memory available, in which case it would be paged out to disk anyway). That way, you can have more than 2GB in memory while only having 2GB adress space. Just found that interesting.

*An incredibly minor and mostly irrelevant nit: on Mac OS X, a 32-bit process's address space is 4GB, not 2GB as commonly found on other OSes.*

