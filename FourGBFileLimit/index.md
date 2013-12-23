---
layout: page
title: FourGBFileLimit
---

If I create a normal document application the system will load files and provide me with an NSData which seems to have been initialized with a memory mapped file.

The problem is that with this scheme there is no way to load files larger than 4 GB (probably less).

Did anyone consider this problem?

*Yes, just tried to load a 2.9 GB file, and it just gave an error requester.*

----

I haven't personally had this problem, but you should be able to override NSDocument's     -readFromFile:ofType: to just get passed the file's path and do your file reading/writing manually.  Both NSFileHandle and the underlying BSD commands support 64-bit file offsets.   -- Bo

----

You will have to do more of the work yourself since the filesystem and the low-level I/O functions supports files larger than 4 GB (I think).  The limitation is that you can't address a chunk of data that big in memory (2 GB address space... or is it 4?  I can't remember if OS X uses signed or unsigned).  As a result, you will have to only read in the parts that you need at any given moment.

This is how DBMSs works.

--JeffDisher

Mac OS X can only address 2 GB of memory, a vital limitation that really needs to be fixed.

----

My point was exactly this, i.e. all "standard" applications will have the limitation, even though it is easy to write 64 bit file size support manually, no-one will do so. They will use the standard mechanism for loading/saving files, and that limit them to 1-2 GB files -- probably very few of these will appear in the comming years (mainly for video, archives and disk images), but they will appear none-the-less, and the NSData abstraction is insufficient to deal with them.

Interestingly, an interface like     std::vector works fine with arbitrary large files, even mutable, preferably using a log-strategy where changes are synchronized to disk on request.

----

If you are dealing with data sets this large you probably you probably don't want to be using NSData anyway. It will be much faster for you to simply read the portions of the file that you need in large chunks. Instead of waiting for VM pager to do it. 

Also, do you really need to have 2> GB of data all in memory ALL the time? Really?
What you are you trying to do?

