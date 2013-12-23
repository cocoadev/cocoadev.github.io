---
layout: page
title: ReadAFilePieceByPiece
---

hi, fellow cocoa programmers.

I'm currently working on an application that deals with very large files (meaning a gigabyte and above). Now, it very often throws me this:

    
vm_allocate(size=1468612608) failed (error code=3)
error: can't allocate region
set a breakpoint in szone_error to debug


Now I guess the only way to avoid this is reading in the file piece by piece. But that's the question. How can I achieve this?

Thanks.
----
Build a 64-bit application and the vm_allocate issue will go away.  Memory mapping a large file and letting the operating system demand page the file with copy on write semantics is almost always the best approach IMHO.

----
It's the best approach unless you want to, say, support hardware older than a year or so. Which most of us probably want to do.

With Cocoa, you can read a file in pieces by using NSFileHandle. For non-Cocoa solutions, you can use C stdio or POSIX.

And just for the record, copy on write and demand paging are unrelated concepts, and unless you actually write to the data that you've read, copy on write never comes into play here.

----

Damand paging means that the operating system will page in portaions of the large file that are currently being read.  Portions that have not been read are never in RAM.  Portions that are no longer being read will not remain in RAM.  The virtual memory system handles everything including mapping the file into the application's address space.

Copy on write means that if you do ever write to a portion of the large file that is mapped into your address space, the corresponding page of RAM is written back to the large file.

This is the optimal way of reading and writing large files in a high performance and resource friendly way.

----
Yes, I know what demand paging and copy on write are. My point was simply that they were implied to be related somehow with the phrase, "demand page the file with copy on write semantics". I realize now that this was just meant to describe the fact that you get both, but it's easy to read it as saying one is done using the other.

As far as "the optimal way", I must violently disagree. It depends entirely on circumstances. It is definitely not the optimal way if your files are larger than a few hundred megabytes and your software runs in 32-bit mode, whether because it runs on 32-bit processors or because it runs on Tiger and uses GUI frameworks. It is probably not the optimal way in many other circumstances as well. The OS is good at optimizing these things, but page faults are extremely expensive and in many scenarios code using read/write and written with the access patterns in mind will go faster and be more clear.

----

Actually, that is NOT what copy on write is.  CoW is when you get a read-only copy of a page which is read/write in the shadow page table so, when you fault on write access, the VM sub-system clones the page and maps the new copy for your process with read/write.  This is used by fork, for example, so you get a copy of the address space without actually copying anything.  If you had a copy on write file mapping, it specifically wouldn't be written back to the file since your write would actually go into the new copy which was not mapped back to the device.  This kind of thing is sometimes use for mmap /dev/zero in case you want to allocate a huge chunk of zeroed memory and have it lazily faulted.

That said (let me try to get back on topic since this page has nothing to do with CoW), a page fault is generally very cheap so mmap is generally the best way to do file I/O (in fact, some operating systems - AIX I seem to recall - implement all file I/O as memory mapped files since it is easy to do and then the VM sub-system will optimize the memory utilization properly for all types of memory.  The problem with mmap for files like this is that you require a contiguous chunk of vmem which isn't always available.  In the case of 32-bit processes, this will generally become a problem between 1.5 and 3 GiB (depending on which OS - and less if you have already loaded lots of libraries or done other kinds of allocations).

----
So how would I go about this in a while-loop? The scenario is this:
I want to read in a large file piece by piece, and with each piece, i want to update the md5 process 
(MD5_Update(&context,(unsigned char *)[myData bytes],[myData length]))
So here's what I came up with:
    
NSData *myData = nil;
NSFileHandle *myHandle = [NSFileHandle fileHandleForReadingAtPath:@"path/to/very/large/file.dmg"];
while (myData = [myHandle readDataOfLength:50000])
{
	MD5_Update(&context,(unsigned char *)[myData bytes],[myData length]);
	[myHandle seekToFileOffset:[myHandle offsetInFile] + 50000];
}

Is this the right way to do it? It still takes a lot of RAM, which indicates that it's not the right way ;)
Thanks for the help!!!

----
No, you're right, but the NSData objects you're creating never get released until your loop is done.  You wind up reading the whole file into disparate chunks of RAM anyway.  Consider flushing the autorelease pool every iteration through the loop.

----
To illustrate:

    
NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
NSData *myData = nil;
NSFileHandle *myHandle = [NSFileHandle fileHandleForReadingAtPath:@"path/to/very/large/file.dmg"];
while (myData = [myHandle readDataOfLength:50000])
{
	MD5_Update(&context,(unsigned char *)[myData bytes],[myData length]);
	[pool release];
	pool = [[NSAutoreleasePool alloc] init];
}
[pool release];


Note that the seek is unnecessary as each read automatically positions the file handle at the end of the read, in position for the next one.

Also I would suggest for best performance that you use a size that's a power of two, such as 65536.

----
Thanks for your input. this is great. one last question. Is it generally better to load in larger pieces (like, say, 10 mb) or smaller pieces (like, say, half a mb)?

----
As always, test and measure the performance if you're unsure.

However, once you get above the size of a page or so (which is 4096 bytes) then the only overhead you're really hitting is system call overhead, since you have to make one syscall per read. I believe that overhead will be totally drowned in the noise well before you reach 0.5MB, so the difference in speed won't be significant.

Also, using more memory has its own performance costs, so I would try to keep it as small as you can while still getting good performance. Measure your own code to determine the optimal size, but my gut feeling would be that something in the range of 8-64kB chunks would be best.


----

If your reading data from a server, larger blocks are always better!  128k-512k seems to be a sweet spot.


----
Alright. Since I'm reading from the HD, I'll give 8-64 KB a try. Thanks.

----
Remember that you're also going to be dealing with the disk's internal buffer, the OS's disk cache, and the C library's buffering.  Your decisions are going to be affected by these as well.

----
So I played around a little bit. The best performance for md5 is when loading 1 MB at a time. So here's the final code how to read in a file piece by piece and use it with the MD5_Update method.

    
NSFileHandle *myHandle = [NSFileHandle fileHandleForReadingAtPath:@"/path/to/some/extraordinarilyhuge/file"];
unsigned long long endOfFile = [myHandle seekToEndOfFile];
[myHandle seekToFileOffset:0];
MD5_CTX context;
unsigned char digest[16];
MD5_Init(&context);
int i = 0;
float count1 = (float)(endOfFile / (float)1048576);
int count = (int)count1;
float overfl = (float)(count1 - (int)count);
if (overfl > 0.0)
{
	count++;
}
for (i=0;i<count + 1;i++)
{
	NSAutoreleasePool *pl = [NSAutoreleasePool new];
	NSData *myData = [[NSData alloc] initWithData:[myHandle readDataOfLength:1048576]];
	MD5_Update(&context,(unsigned char *)[myData bytes],[myData length]);
	[myData release];
	[pl release];
}
MD5_Final(digest,&context);
NSData *md5Data = [[NSData alloc] initWithBytes:digest length:sizeof(digest)];
NSMutableString *aString = [[NSMutableString alloc] initWithCapacity:32];
unsigned int a,cnt = [md5Data length];
unsigned char *bytes = (unsigned char *)[md5Data bytes];
for (a=0;a<cnt;a++)
{
	[aString appendFormat:@"%02x",bytes[a]];
}
[md5Data release];
NSLog(aString);
[aString release];

It works very well and is faster than the terminal's md5 command. That's what I found, at least. Thanks for everybody who contributed to this solution. A 7 GB file took 3:13 with this code. The Terminal's md5 command calculated for 3:50.
Take care,

--MatthiasGansrigler
----
Youre being wasteful here.  You're seeking to the end of the file only to seek all the way back to the beginning.  While I'm sure there are low-level optimizations involved that make this trivial, it's still a bad idea.  The worst thing that could happen is that the filesystem might need to visit every sector the file occupies to determine its length!  Why bother calculating the length of the file so early if you know that the     NSData object returned from     -[NSFileHandle readDataOfLength:] is only going to be actual data from the file, regardless of whether or not     current file pointer + max length > size of file?  Bonus is you can forget about your whole messy division thing.

Secondly, you seem to have missed the mark with the autorelease pool.  You're almost there, but not quite.

    
for (i=0;i<count + 1;i++)
{
	NSAutoreleasePool *pl = [NSAutoreleasePool new];

	// Why do this?  It makes no sense.  All you're doing here is copying an autoreleased object over to a manually-retained object.
	// This means you still have to worry about the autorelease pool (or else the autoreleased objects will accumulate just like before)
	// but you *also* have to manually release your myData object!  Needless.
	//
	// NSData *myData = [[NSData alloc] initWithData:[myHandle readDataOfLength:1048576]];
	//
	// Do this instead.
	NSData *myData = [myHandle readDataOfLength:1048576];

	MD5_Update(&context,(unsigned char *)[myData bytes],[myData length]);

	// Not necessary anymore.
	// [myData release];

	[pl release];
}

----
Well, ok, I get the autoreleasepool thing. But what about the for-loop? I need to know the length of the file to determine how often I have to "go through" the file to get all the data piece by piece? What would you suggest?

----

Read the documentation for     -[NSFileHandle readDataOfLength:].  It returns an empty NSData object on end-of-file.  You can replace the entire division, counter, for-loop type construct with this:

    
NSAutoReleasePool *arp = [[NSAutoReleasePool alloc] init];
NSData *chunk = [myFileHandle readDataOfLength:DATA_READ_SIZE]; // Please, NEVER hardcode constants like this!
while([chunk length] > 0)
{
    // Do MD5 stuff here...

    [arp release];
    arp = [[NSAutoReleasePool alloc] init];
    chunk = [myFileHandle readDataOfLength:DATA_READ_SIZE];
}
[arp release];

// Just to make things perfectly clear...
arp = nil;
chunk = nil;  // This has been autoreleased by releasing the autorelease pool.


If you've ever taken a computer science class, or worked with any modern programming environment, you should already know that by definition any for loop can be converted into a while loop, and vice versa.  This is easily proven by induction.

