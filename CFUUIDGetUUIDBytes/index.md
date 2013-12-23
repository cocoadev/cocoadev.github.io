---
layout: page
title: CFUUIDGetUUIDBytes
---

Taken from Apple's Online Documentation:

*CFUUID objects are used by plug-ins to uniquely identify types, interfaces, and factories. When creating a new type, host developers must generate UUIDs to identify the type as well as its interfaces and factories.


UUIDs (Universally Unique Identifiers), also known as GUIDs (Globally Unique Identifiers) or IIDs (Interface Identifiers), are 128-bit values guaranteed to be unique. A UUID is made unique over both space and time by combining a value unique to the computer on which it was generated�usually the Ethernet hardware address�and a value representing the number of 100-nanosecond intervals since October 15, 1582 at 00:00:00.


The standard format for UUIDs represented in ASCII is a string punctuated by hyphens, for example 68753A44-4D6F-1226-9C60-0050E4C00067. The hex representation looks, as you might expect, like a list of numerical values preceded by 0x. For example, 0xD7, 0x36, 0x95, 0x0A, 0x4D, 0x6E, 0x12, 0x26, 0x80, 0x3A, 0x00, 0x50, 0xE4, 0xC0, 0x00, 0x67 . To use a UUID, you simply create it and then copy the resulting strings into your header and C language source files.


You can create a CFUUID object, and thereby generate a UUID, using any one of the CFUUIDCreate... functions. Use the CFUUIDGetConstantUUIDWithBytes function if you want to declare a UUID constant in a #define statement. You can get the raw bytes of an existing CFUUID object using the CFUUIDGetUUIDBytes function.*

Here's a category I use:
    
@implementation NSString (UUID)
+ (NSString*)stringWithUUID
{
   CFUUIDRef uuid = CFUUIDCreate(kCFAllocatorDefault);
   NSString* str = (NSString*)CFUUIDCreateString(kCFAllocatorDefault, uuid);
   CFRelease(uuid);
   return [str autorelease];
}
@end


GREAT Category!  Just what I needed! Thanks!

----

alternatively, check out the -globallyUniqueString method in NSProcessInfo. as of Panther, this returns a string in the same format as CFUUID. *--boredzo*

----

Remember that globallyUniqueString does not say that the length will be fixed. CFUUIDGetUUIDBytes provides a 128 bit length identifier. --zootbobbalu

----
Dec-2005
OK so I have a question about either of these: globallyUniqueString or CFUUIDCreateString

Is there a way given the results from either that you could determine that a specific machine was (or was not) capable or rendering that result?
I am aware that state changes like motherboard swap outs can fiddle the results so accepting that risk: can I determine that a machine, in its current state was capable of either result?  I've looked at the RFC, the reading of which I am a mere mortal, and it appears that there a 3 ways to generate UUIDs 1)MACAddress 2)random number generation 3)hashing.  All three also have a deep entanglement with the machine's clock.

So my hunch is that it really isn't possible unless you get lucky enough the bump into the MAC algorithm.  But as a general statment, given a UUID from some unknown source and a specifc Macintosh, it really isn't possible to tell if the UUID could have come from that machine.  YES??   Thought I would ask before heading off in other directions....
 
----
If your goal is to have some kind of identifier to identify a machine, you're barking up the wrong tree. Use UUIDs for their intended purpose, which is being universally unique identifiers. If you want to generate a number that can identify a specific Mac, then there are other techniques you should use. I believe Apple has a technote on what kind of values you could use to identify a Mac and how to get them, but the general conclusion is that this is Hard and Unreliable.

If you already have a UUID coming from somewhere, and you want to see if it came from a machine, you may still be doomed. However, the node field might contain the machine's MAC address, so you could check that.

