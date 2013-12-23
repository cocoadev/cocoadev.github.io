---
layout: page
title: FileSystemQuestion
---

I'm working on an app that is very disc intensive and I have data files that are indexed. I was wondering if anyone knows if HFS+ tries to keeps files in the same directory group in the same area of the hard disc. The latency caused by head travel is very noticible in the performance of my app, and I would like to keep seek times as low as possible. But part of my design problem is that the indexed portion of the file and the data portion of the file vary in size dynamically, so one method for reducing the number of truncations is to have an index file and a separate data file. The obvious concern with having two separate files is the possibility that the file system might move these related files far apart from each other on disc. 

Any ideas??

----

Just curious for personal interest, how are you timing this? I mean, it's really the head travel that's causing the latency? (Again, not disagreeing, just think it's cool.)

----

I'm basing this totally on the fact that files not in disc cache take an average 10+ milliseconds to open (I have timed this by first scrubbing through a 500MB unrelated file to clear cache and then randomly openning files and reading the first 4096 bytes). I haven't profiled the disc performance related to files contained in the same directory, but since HFS is based on a tree file structure I just thought that HFS+ might be doing more than just "clumps".

----

HFS+ doesn't do anything to keep files in a directory together. HFS+ doesn't periodically defragment or optimize file locations (according to Apple, their performance tests claim that it doesn't need to). The information inside the catalog file is kept together, but since that generally is small and hot, it stays in the cache and you don't have to worry about it.

The actual file data (the extents) will pretty much stay wherever it happens to be when you allocate it. Simplistically, you can imagine that file allocations are laid down in the same order they are requested. Because of this, the optimal case for reading is when you're reading in exactly the same order that you wrote the data. (That's a simplification, but it's basically true modulo a few details. Detail 1: files grow in clumps, sometimes pre-allocating space before you've explicitly requested it.  Detail 2: obviously it doesn't apply if other files are in the way, or if there are files being allocated by other processes at the same time, etc etc.) 

If you write/grow more than one large file at a time, they will be fragmented on the disk and interleaved with each other. If you then read just one of them from start-to-finish, you'll spend a lot of time seeking as you skip across the blocks that were allocated for the other file(s). 

How can you avoid this fragmenting? Well, if you know the size of the files you're going to write beforehand, or even an upper bound on their sizes, you can do a greedy allocation and grow the files to their maximum size as soon as you create them. This can get around the fragmenting/interleaving problem. You can do this with a SetEOF in the Carbon File Manager. I'm not sure of the Cocoa equivalent but after browsing a few man pages, it'd probably be something like this. (and btw, tossing this into a category on NSFileHandle might be cleaner...)

    
- (off_t) preallocateSpace:(off_t)bytes inFile:(NSFileHandle*)fh
{
    fstore_t fst = {};
    fst.fst_flags = F_ALLOCATECONTIG;
    fst.fst_posmode = F_PEOFPOSMODE; // add to current physical EOF
    fst.fst_offset = 0;
    fst.fst_length = bytes;
    
    if (fcntl([fh fileDescriptor],F_PREALLOCATE,&fst) != 0)
        NSLog(@"fcntl(%d,F_PREALLOCATE,%lld bytes) failed! errno = %d",
                [fh fileDescriptor], (long long)bytes, (int)errno);

    return fst.fst_bytesalloc; // return number of bytes actually allocated
}


I haven't tested the above code but it's probably not far off ...
 -- DrewThaler

----

Thanks Drew! You answered one important question that will enable me to reduce the number of options to consider. The greedy allocation was and still is an option, but obviously I would like to avoid doing this. After reading your reply, I think I'll be using a conservative allocation and accept the performance hit cause by the extra truncations. 

----

Note that 10.3 introduced automatic file defragmentation - if a file is fragmented and <20MB long, it is defragmented on the fly when it is opened. I assume this feature remains in 10.4 and 10.5. --GC

