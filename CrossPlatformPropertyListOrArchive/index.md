---
layout: page
title: CrossPlatformPropertyListOrArchive
---



I'm writing an application that saves settings using Cocoa's archiver, which I found to be extremely useful and easy for this. I'd like to eventually write a Windows/Linux/Etc version that could read the Mac settings and vice-versa. Is there an type of module available for cross-platform reading of Cocoa plist settings/archives, or would I have to parse the file (since it's all XML in the end) manually instead? -Seb

*As long as the archiver's format is set to     NSPropertyListXMLFormat_v1_0, you're correct that it's XML. I don't know whether there's a ready-made solution that makes it easy to parse XML documents formatted with Apple's PLIST DTD. You might want to google it. If you *really* want cross-platform ease-of-use, you may want to consider (I shudder to say this) REALbasic over Cocoa. Or even Java. REALbasic makes it pretty easy, though, in this case. You write the application and check off boxes for any of the three platforms you mentioned above and an executable is created for each on build. Their XML additions of late (so I hear) are pretty good, so this should make your task easy.*

Well, my 'application' is really more like a plug-in for another app, so it's not stand-alone, but does have it's own preferences and settings files, so RealBasic (shudder) isn't really an option for my, neither is Java. I'm just trying to 'think ahead' of how I could access existing settings on other platforms that might not have the same easy tools as Cocoa provides for this.

*Then Apple's PLIST DTD is as good as any other, IMO. It's just that you'd have to either find a ready-made parser for the target platform or roll your own. As far as I know, there's really no specific 'cross-platform' format. That's what XML is for.*

----

Apple has cross platform plist reading/writing code available as sample code, I believe.  I'll try to find it.

I've also seen a python implementation.

----

Simplest and fastest solution would be to write your own XML format in straight C/C++ using TinyXml, and then you already have your cross-platform format!

----

This is probably what I was thinking of: http://developer.apple.com/darwin/cflite.html

Simple Python and Java implementations also turned up with one google search.

----
What about the new binary format for PLIST introduced with OS X 10.4?

As far as I know it is not documented, so you should avoid it if you are writing cross-platform data files. CFLite might be able to read them, but even if it does, you'll be stuck using CFLite forever if you take that path.

*I'd also advise against the binary format for simple readability concerns. Personally (as a user), I *prefer* my plists to be plain text so I can easily peruse them.*

----
There is no new binary format for plists AFAIK. What has happened is that preference plists are now written in their binary form by default, whereas they would be written in the XML form previously. Other plists (or rather, typed archives) would normally be written in binary for ages.
I've made very good experiences with GNUstep regarding plists and keyed archives: All the simple classes (de)archive correctly on my windows box. I use the standard keyed archivers on both mac and windows side, and as long as the classes archive the same stuff (i.e. same keys, same contents), everything just works.
Note that you can easily convert from binary to normal format for simple plists (with plutil), so that is not really a problem.

As far as I know, GNUStep's archivers are completely incompatible with Apple's, so if you plan on using them for cross-platform file compatibility, then you'd better use GNUStep's all the way through. Perhaps this has changed and I'm not aware of it.

The existence of plutil doesn't change a thing with this question. A closed and undocumented format is still closed and undocumented even if it's easy to convert to something open. You'll not find plutil on a random Windows or Linux box, after all.

"It is not clear who wrote what in the above paragraph. It cannot be written by the same person, since the lower part directly contradicts the rest. For the record, GNUSteps archivers are completely compatible with Apple's, as long as you use the XML representation in both cases (that does not mean that an object unarchives easily between the two: that depends on the implementation of the object, not the archiver). The existance of the binary format is orthogonal to the issue at hand: if you want cross-platform compatibility, just don't use the binary format (of course, with the binary format code being open source, one could easily argue that it is, in fact, open). Being able to easily convert between the "open" (XML) and the "perceived-as-not-open" binary format is just an added bonus."

----

You could also look into the (Tiger / 10.4 only) NSXML: http://developer.apple.com/documentation/Cocoa/Conceptual/NSXML_Concepts/index.html

----

The following is provided in the CF-lite file "CFBinaryPList.c":

    
/*
 HEADER
	magic number ("bplist")
	file format version
 
 OBJECT TABLE
	variable-sized objects
 
	Object Formats (marker byte followed by additional info in some cases)
	null	0000 0000
	bool	0000 1000			// false
	bool	0000 1001			// true
	fill	0000 1111			// fill byte
	int	0001 nnnn	...		// # of bytes is 2^nnnn, big-endian bytes
	real	0010 nnnn	...		// # of bytes is 2^nnnn, big-endian bytes
	date	0011 0011	...		// 8 byte float follows, big-endian bytes
	data	0100 nnnn	[int]	...	// nnnn is number of bytes unless 1111 then int count follows, followed by bytes
	string	0101 nnnn	[int]	...	// ASCII string, nnnn is # of chars, else 1111 then int count, then bytes
	string	0110 nnnn	[int]	...	// Unicode string, nnnn is # of chars, else 1111 then int count, then big-endian 2-byte uint16_t
 0111 xxxx			// unused
	uid	1000 nnnn	...		// nnnn+1 is # of bytes
 1001 xxxx			// unused
	array	1010 nnnn	[int]	objref*	// nnnn is count, unless '1111', then int count follows
 1011 xxxx			// unused
 1100 xxxx			// unused
	dict	1101 nnnn	[int]	keyref* objref*	// nnnn is count, unless '1111', then int count follows
 1110 xxxx			// unused
 1111 xxxx			// unused
 
 OFFSET TABLE
	list of ints, byte size of which is given in trailer
	-- these are the byte offsets into the file
	-- number of these is in the trailer
 
 TRAILER
	byte size of offset ints in offset table
	byte size of object refs in arrays and dicts
	number of offsets in offset table (also is number of objects)
	element # in offset table which is top level object
 
 */


�BrentGulanowski

----

Two implementations of binary plist reading can be found at http://paste.lisp.org/display/52154 . Neither has been tested extensively. The first is known to be buggy � it doesn�t get the offset for the root object in the correct way.

� JensAyton

The (apparently) finished version of those two implementations has been integrated into CocoTron and can be found in its repository here:

http://cocotron.googlecode.com/svn/trunk/Foundation/NSPropertyList/NSPropertyListReader_binary1.h
http://cocotron.googlecode.com/svn/trunk/Foundation/NSPropertyList/NSPropertyListReader_binary1.m

-- MikeAsh

