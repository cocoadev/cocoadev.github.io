---
layout: page
title: ReadingBinaryFIles
---



I have a binary file that represents some information (surprise!) and I would like to parse its values into an NSDictionary for use in my Cocoa application. I have the C code necessary to parse the file, however I want to do it using Cocoa. 

For starters, I need to validate the file... the file has a signature in the first 4 bytes, such as "\0BOB". 

How can I get this value and compare it, so I know it is the right format of a file?

I've tried a bunch of things with NSData to no avail :-/

----

     

NSData * fileData = [NSData dataWithContentsOfFile:@"myfile.bob"];
if(memcmp([fileData bytes], "\0BOB", 4) != 0) {
    //complain
}

 
--DavidVierra (clarification by KritTer)
----
The above in addition to ensuring that the length of the read data is indeed at least 4 bytes beforehand.

If there is a predefined structure for your format, you can also assign a pointer to one and use it for validation.

     
enum { MYFORMAT_SIG = '\0BOB' };

typedef struct MYFORMAT {
	uint32_t signature;
	// additional members here.
	// watch for alignment issues.
} MYFORMAT;

.
.
.

if ([fileData length] >= sizeof(MYFORMAT))
{
	const MYFORMAT *const pData = [fileData bytes];
	if (NULL != pData && MYFORMAT_SIG == pData->signature)
	{
		// PARTY ON THE STRUCT...
	}
}
 

The enum works as your signature is 4 bytes...

----

Question: When using the line above     	const MYFORMAT *const pData = [fileData bytes]; does that put the first n bytes from     [fileData bytes] into pData where n is     sizeof(MYFORMAT)?

----
No, It is just treating the pointer returned from      [fileData bytes]  as a pointer of type MYFORMAT. It does not copy any data whatsoever.

----

Ah! I thought there might be a more Cocoa way of doing it, but this is basically the same as my C code... however it does help! Thank you!

----

Objective-C is a pure superset of C. Never be afraid to use that fact. If you have a C way of doing things that does the job well, by all means use it.


----

I much prefer Objective-C only, wherever possible :)

Now, I've come to a stumbling block :-/

    
/* For endian neutrality */ 
u_int32_t le32(u_int32_t bits) 
{ 
� �u_int8_t *bytes; 
� �bytes = (u_int8_t*)&bits; 
� �return(bytes[0] | (bytes[1]<<8) | (bytes[2]<<16) | (bytes[3]<<24)); 
} 


There is this function, I don't think it works properly on OS X/PPC. Any ideas why? Also have an le16...

----
Looks like a byte-order flip to me.

If this code is being ported from an little-endian architecture you can probably just stub these out for big-endian architectures such as the PowerPC. There are standard posix functions for performing these tasks as well, take a look at htonl, htons, ntohl, and ntohs (again, no-ops on PowerPC).

*Don't forget the _lhbrx and _lwbrx functions defined in ppc_intrinsics.h.  If you need to byteswap from little-endian format, they will do a load and a byteswap in a single instruction.*

