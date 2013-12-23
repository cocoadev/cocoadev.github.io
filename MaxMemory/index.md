---
layout: page
title: MaxMemory
---



I'm confused about the available memory. When running my Cocoa application I get a crash because it ran out of memory.

It is a multithreaded application and the spot where a lot of memory is needed is not used by objects but simple malloc() calls in the second thread.

OK, I discovered that I can only use about 1.8 GB of memory (terminal >> top) and as far as I can tell every application can use 4Gb in Tiger.
So, I created a little application to test this (code below) and indeed once again I can only allocate about 1.8Gb. Could it be that I lose so much because of aligning the memory?

Second question:
I can not recover from this out of memory. I can not use NSLog() or whatever to give a nice message. The program crashes.
I can not even use a safety buffer which I release as soon as I run out of memory because even this call to free() makes the application crash.

So, I don't understand this and I'm hoping someone will clear things up.

BTW: when I compile for 64 bit, things are different, my drive gets full and I have of course plenty of memory with my G5 :-)

    
#include <CoreFoundation/CoreFoundation.h>
int main (int argc, const char * argv[]) 
{
	long size = 0;
	void *buf;
	do
	{
		buf=malloc(272);
		if ( buf==nil)	{
			printf("sizel %d %f GB",size, (double)size/(1024.0*1024.0*1024.0));
			exit(0);
		}
		else
			size+=272;
		}while(1);
    return 0;
}


----

I've confirmed your results on my machine. Further, I've discovered that the memory areas skipped are actually large contiguous chunks of 0x100010 (1048592) bytes, skipped quite regularly every 0x200000 (2097152) bytes, rather than any kind of alignment problem. That is, half of the available memory addresses are being skipped over.

Weird.

I wonder if it is some side-effect of the way     malloc stores valid addresses? Perhaps some hashtable oddity?

The entire memory range from 0x90000000-0xb0000000 is also unavailable. I guess it's reserved for the stack.

If you want to max out your memory usage, allocate large chunks -- say, 0x0FFFFFFC (268435452) bytes -- and split them up into usable chunks yourself. I got 3.42GB memory usage that way. You could even go straight to the kernel and ask for memory pages directly. -- KritTer

----

malloc, like all things computery, enjoys powers of two. When you ask malloc for 272 bytes, it probably allocates 512. This explains why so much goes missing.

Also, you're using a long to store size. This will overflow into the negative once you hit 2GB.

Out of memory errors are pretty much impossible to recover from on a UNIX. Sometimes they are even impossible to *detect*, as they just result in your program segfaulting when you try to touch some memory that was promised but can't be delivered.

----
Or you could read my post, where I said it's not an alignment problem. I *did* check that :) I also changed the code to use a long long, so overflow would be detected, and stated above where the memory was going. -- KritTer

----
Yeah, it appears you're right. I guess being a multiple of 16 is enough.

