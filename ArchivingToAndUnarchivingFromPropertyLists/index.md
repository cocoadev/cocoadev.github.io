---
layout: page
title: ArchivingToAndUnarchivingFromPropertyLists
---

To archive and unarchive from property lists, use the Foundation class NSPropertyListSerialization, available in 10.2 and later.

Property lists are also used for nib files, keyed archives, and in methods such as -[NSDictionary writeToURL:atomically:], but this should be regarded as an implementation detail.  A dictionary saves itself in its own format to disk, which happens to today be a plist.  Similarly, -[NSDictionary description] returns a plist (provided all the objects in the dictionary can be written to a plist), but that shouldn't be relied on.  There is no guarantee that the description of an object won't change between system versions.  description methods are not for parsing, or really anything other than debugging.  

In short, if you're working with plists explicitly, you want to use NSPropertyListSerialization.

----

Mac OS X 10.2 includes the ability to use keyed archiving to archive to XML format files. - ChrisMeyer

----

In a post to the MacOsxDev mailing list, Mark Onyschuk wrote: 

I'm sure that this sort of thing is one of the frameworks in the "bag of tricks" of many Cocoa developers. We'd developed the same sort of thing for GLYPHIX 1.0 back in the Mac OS X Server days, and we published the source:

ftp://ftp.oaai.com/pub/frameworks/OAPropertyListCoders.1.0.s.tar.gz

...[Y]ou can't just add a new NSCoding style coder to handle property lists because these lists require key/value pairs rather than a simple series of values. OAPropertyListCoders defines a protocol:

    
@protocol OAPropertyListCoding <NSObject>
- (id)initWithPropertyListCoder:(OAPropertyListCoder *)aCoder;
- (void)encodeWithPropertyListCoder:(OAPropertyListCoder *)aCoder;
@end


which works very similarly to the     encodeWithCoder:/initWithCoder: pair that everyone's familiar with. You should find that adding property list coding to your app is somewhat simplified if you use this framework.

----
Actually, a property list can be an array; try using NSArray's     writeToFile:atomically:. Conversely, NSCoder does allow key/value pairs; just think about NSKeyedArchiver and NSKeyedUnarchiver (the preferred style these days). It's only if you need pre-10.2 support that this matters.

----
"A series of values" can also be used to implicitly generate keys by, for example, incrementing a counter and using the counter's value for the key. This is how MAKeyedArchiver, which internally only understands keyed coding, supports the non-keyed APIs. This is handy because it allows you to archive Cocoa classes without writing your own coder support methods for them, and without relying on them having keyed coding.

----

There's also something at http://www.metaobject.com/downloads/Objective-C/Objective-XML-2001.9.21.tar.gz

----

See also PropertyList.

