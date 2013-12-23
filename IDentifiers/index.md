---
layout: page
title: IDentifiers
---

A UUID is a 128-bit value, with an interchangeable string equivalent, that is guaranteed to be unique across the space of all UUIDs generated using the same algorithm on any computer anywhere, and unique across time until approximately 3400 A.D. A UUID is useful as a unique key in databases, and in many other contexts. Standards-based.

----

Now... how in the holy hell is *that* supposed to work? (JeffHarrell)

*
It uses a combination of your MAC address and a form of the current time. According to the spec, you can generate something like 10 million UUID's per second. Read more here: http://www.ietf.org/internet-drafts/draft-mealling-uuid-urn-03.txt
*

----

The following CoreFoundation function is provided to create UUIDs as required:

http://developer.apple.com/documentation/CoreFoundation/Conceptual/CFPlugIns/Tasks/GeneratingUUID.html

----

Since a UUID is guaranteed to be unique, it can be used to generate a unique filename or create a folder in a shared environment.

Note however that it does contain your MAC address in it; this caused Microsoft some trouble when it was embedding UUIDs in word documents. *This has been addressed in a security update for Panther (at least, maybe Jaguar) and no longer contains the MAC address.*

----

    
+ (NSString *)stringWithUUID
{
	CFUUIDRef uuid = CFUUIDCreate(kCFAllocatorDefault);
	NSString * str = (NSString*)CFMakeCollectable(CFUUIDCreateString(kCFAllocatorDefault, uuid));
	CFRelease(uuid);
	return [str autorelease];
}


----

    
[[NSProcessInfo processInfo] globallyUniqueString]


[http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSProcessInfo.html]

----

Question: why isn't there a uuidgen or similar tool available either in XCode or for the command line?  Why should we have to write code to do this common task?  Well, I wrote it once and created an executable.

----

First, uuidgen is available in Mac OS X. Second, do you consider calling CFUUIDCreate() writing code? --KonstantinAnoshkin

----

Except -globallyUniqueString is guaranteed to be "unique for the network" (to quote the docs) and is allowed to contain almost any character. A UUID is guaranteed across any computer on any network at any time. globallyUniqueString also doesn't guarantee the length or nature of the returned string, while a UUID has a prescribed format. It depends on how rigourous your needs are.

----

A simple category for NSString for generating UUID's: [http://gist.github.com/486765]

