---
layout: page
title: RegexKit
---



Announcing RegexKit - A framework for regular expressions using the PCRE library.

Edited on 01/29/2008 to reflect version 0.6.

Version 0.6 Beta has been released.


* 0.6 upgrades PCRE to version 7.6, which includes an important security related buffer over flow fix.  Users are encouraged to upgrade. In addition to upgrading to PCRE 7.6, RegexKit 0.6 is now compiled with the Xcode 3.0 GCC compiler feature '-fstack-protector-all' which helps catch and prevent stack related buffer overflows and exploits.


New features in 0.6:


* Upgraded to PCRE version 7.6, which includes an important security related bug fix.
* Preliminary support for internationalization, though English remains the only provided localization.
* Support for returning NSError objects for error conditions.
* Support for multiple regular expressions in a collection (i.e., NSArray) to be evaluated concurrently on multiple threads, one per CPU.


The project is hosted at sourceforge, and you can access it via:

http://regexkit.sourceforge.net/

The framework documentation is available at:

http://regexkit.sourceforge.net/Documentation

The sourceforge project page is:

http://sourceforge.net/projects/regexkit/

I would appreciate feedback from experienced cocoa developers regarding the API and usability of the framework.

Some highlights:


* BSD license for the framework.  The PCRE library has a BSD license as well.
* Uses PCRE 7.6, the latest release as of 01/29/2008.
* Mac OS X 10.4 or later required.
* Caches compiled regular expressions for speed.
* Specifically designed to be multithreading safe.
* Designed to be fast and light-weight.  Uses the stack extensively for temporary items and work, rarely resorts to malloc().
* Not just a wrapper around PCRE, but extends by category NSArray, NSData, NSDictionary, NSSet, and especially NSString.
* It's documented.  No, I'm not kidding, it's actually documented.  Documentation style is the same, familiar style as Apples documentation. Integrated Xcode 3.0 DocSet documentation format also available.
* Makes extensive use of CoreFoundation for maximum speed.
* Also supports GNUstep. CoreFoundation calls are replaced with their equivalents in Foundation.
* Support for Mac OS X 10.5 Leopard.
* Dual mode support for Garbage Collection and traditional retain / release.
* Full 64 bit support on Mac OS X 10.5.  Pre-built framework binary includes the following architectures: ppc, ppc64, i386, x86_64.
* Documentation available in Xcode 3.0 DocSet format.
* Collection of instruments for Instruments.app.
* RegexKit specific DTrace probe points.


Garbage Collection is completely optional, like the rest of Cocoa.  It is dynamically activated at load time if the Cocoa NSGarbageCollector is active.  Otherwise the standard retain / release style of managing memory is used.

The 32 bit architectures (ppc, i386) require Mac OS X 10.4 or later, while the 64 bit architectures require Mac OS X 10.5 or later.

The frameworks documentation is now integrated with Xcode 3.0, just like the rest of the Apple documentation.  With the Research Assistant open, placing the editors insertion point over a RegexKit method will bring up that methods documentation, including related methods.  You can click on any of these and Xcode will open the corresponding page in the documentation viewer.  The documentation viewer also offers full keyword searching of the entire documentation.

As an example of that's possible with the NSString additions, consider the following:  Find, extract, and convert to an unsigned int a hex value from a NSString.  Here's how you can do it with RegexKit:

    
unsigned int hexValue = 0;

[@"Conversion color: 0xFF0000, Order: 1" getCapturesWithRegexAndReferences:@"color: (0x[0-9a-fA-F]+)", @"${1:%x}", &hexValue, NULL];
// hexValue == 16711680 || 0xFF0000


getCapturesWithRegexAndReferences: allows you to easily match a capture subpattern from a regular expression and perform a scanf() style conversion.  Also note that there is no need to create a regular expression object, the framework will automatically convert NSString objects to RKRegex objects for you.  In fact, getCapturesWithRegexAndReferences: will accept either an instantiated RKRegex object, or a NSString which it will convert.

You can also create new strings with references to regular expression matched text:

    
NSString *newString = [@"Doe, John" stringByMatching:@"(?<last>\\S+),\\s*(?<first>\\S+)" withReferenceString:@"Dear ${first} ${last},\n\nHow are you today, ${first}?"];
/*
newString ==
Dear John Doe,

How are you today, John?
*/


There are similar search and replace methods as well.

With this alpha release, I'm looking for comments from objective-c cocoa developers regarding the API.  Any other comments are welcome as well.  I'm in the final push to get a "1.0" general release done, so I'd like to freeze the features of the framework and concentrate on getting a polished release out the door.  As I mentioned, there's some conflicting information in the current release regarding first-time user related information, such as references to an old "cli_test*" target/code.  I don't expect any experienced cocoa developer to be thrown off, though.  The documentation regarding adding the framework to your project (should?) be just fine, just no examples yet.

----

How does this compare to OgreKit? --*boredzo*

----

I will try to keep this as unbiased as possible, and I encourage others to make any changes necessary to keep it objective.  That said, as a full disclosure, I am the author of RegexKit. I'm probably the only person who can speak for it right now, since it has just been released.  I also know very little about OgreKit.  Take the following with however large a grain of salt you feel is warranted.

*Regular Expression Engine*

RegexKit


* PCRE - Perl Compatible Regular Expressions.  http://www.pcre.org/
* Version used: pcre-7.3
* Easily upgraded: Yes, change the Xcode build setting **PCRE_VERSION** and rebuild. Assumes there are no "major" changes.
* Includes regex engine in distribution: No.  Configured **PCRE_VERSION** is downloaded and built.
* Library built as a Universal Binary: Yes


OgreKit


* Oniguruma. http://www.geocities.jp/kosako3/oniguruma/
* Version used: oniguruma ver.4.0.1
* Easily upgraded: Unknown.
* Includes regex engine in distribution: Yes.
* Library built as a Universal Binary: Yes


*License*

RegexKit


* RegexKit Framework covered by a BSD license. http://regexkit.sourceforge.net/Documentation/RegexKitProgrammingGuide.html#LicenseInformation
* Regular expression engine (PCRE) covered by a BSD license. http://www.pcre.org/license.txt


OgreKit


* OgreKit Framework covered by a BSD "like" license. http://www8.ocn.ne.jp/~sonoisa/TiddlyWikiPod/OgreKitLicense.txt
* Regular expression engine (oniguruma) covered by a "like" BSD license. http://www8.ocn.ne.jp/~sonoisa/TiddlyWikiPod/OgreKitLicense.txt


*Overall*

RegexKit


* Universal binary: Yes.
* Minimum Mac OS X version required: 10.4.


OgreKit


* Universal binary: Yes.
* Minimum Mac OS X version required: ppc, 10.3.  i386, 10.4.


RegexKit strengths


* Can be used with GNUstep.
* Designed to be multithreaded safe.
* Caches the compiled regular expression.  The cached expressions are fully multithreading safe.
* Unicode support.  The PCRE library is built with "--enable-utf8 --enable-unicode-properties". The framework tries to keep the original encoding of the source strings during operations and derived strings inherit their parent encodings.
* Well documented. Documentation language: English.  http://regexkit.sourceforge.net/Documentation
* On Mac OS X, uses Core Foundation where possible to speed operations.
* Includes regex operations on objects other than NSString.  For example, NSDictionary has methods such as: dictionaryByMatchingKeysWithRegex:, objectsForKeysMatchingRegex:, etc.


During development, a strong effort was made to keep the framework light weight and fast.  No modifications are made to the regular expression engine in terms of performance, instead the tuning effort was focused on keeping the overhead to enable object-oriented access to pcre at a minimum.  Examples include:


* Regular expressions are cached.
* When matching against NSString objects, the pointer to the objects backing store is used whenever possible.  This keeps the overhead of creating and disposing of temporary strings (such as [string UTF8String]) to a minimum.
* Nearly all operations were written to use the stack for temporary storage.  Stack allocations are extremely fast, requiring no dynamic memory allocation (malloc) overhead, and reducing the possibility of memory leaks.
* When performing operations such as "search and replace all occurrences", a buffer on the stack is used to record all the required copy operations to assemble the final string.  Only after all the matches have been found is the final string assembled, requiring only a single malloc(), which is passed to the appropriate NSString method so no additional buffers are created.


RegexKit wild claims


* Does not leak memory.  There is nearly 400Kbytes of unit tests, one of which is executing most of those tests randomly from multiple threads while randomly clearly the regular expression cache with a lot of the "debugging knobs" turned all the way up (linked against _debug, malloc scribbling, core foundation scribbling, MallocStackLogging, etc).  This includes all the exception tests, such as init... with inputs that throw an exception.  These tests actually found long standing bugs in the malloc library.


RegexKit weaknesses


* Untested and extremely immature.  Released 8/31/2007.
* Unicode support, while present, is largely untested.  The author is an native English speaker and has no experience dealing with unicode strings.
* Currently has no localization.  Everything is in English.


OgreKit strengths

Sorry, I'm just not familiar with OgreKit to write up anything comprehensive here.


* Much more mature.


----

A lot of regex classes provide ways to get an array of pattern catches.  I see that RegexKit allows for named catches and "${1}" captures but what if I don't know how many captures a regex will produce?  Is there a way for RegexKit to do this?

Answering my own question:
    
@interface NSString (RKAdditions)
- (NSArray *)capturesForRegex:(NSString *)regexstr;
@end

@implementation NSString (RKAdditions)
- (NSArray *)capturesForRegex:(NSString *)regexstr
{
	RKRegex *regex = [RKRegex regexWithRegexString:regexstr options:RKCompileNoOptions];
	NSData *data = [self dataUsingEncoding:NSUTF8StringEncoding];
	NSRange *ranges = [regex rangesForCharacters:[data bytes] length:[data length] inRange:NSMakeRange(0, [data length]) options:RKMatchNoOptions];
	NSMutableArray *captures = [NSMutableArray array];
	if (ranges != NULL)
	{
		unsigned int idx = 0, count = [regex captureCount];
		for (idx = 0; idx < count; idx++)
			[captures addObject:[self substringWithRange:ranges[idx]]];
	}
	return captures copy] autorelease];
}
@end


This works fairly well so far.  The first capture is the entire matched string.  Anyone see any improvements?  -G

----

One improvement: If rangesForCharacters is NULL then don't iterate through the ranges. -hac 

----

Is there any recommended application to generate / test on input Regex complient with [[RegexKit ?

----

There is now; Voila: http://atastypixel.com/blog/reginald-regex-explorer/

