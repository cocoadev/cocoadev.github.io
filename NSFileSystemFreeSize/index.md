---
layout: page
title: NSFileSystemFreeSize
---

NSFileSystemFreeSize

One of the keys in the NSDictionary object containing file system attributes. Returns a file systems free size in bytes (doc. says "usually"?).

Usage example:

    
NSFileManager *manager = [NSFileManager defaultManager];
NSDictionary *fsattrs =  [manager fileSystemAttributesAtPath:@"/path/to/somewhere"];

NSLog(@"Free space %qu", fsattrs objectForKey:[[NSFileSystemFreeSize] unsignedLongLongValue]);


----

I filed a bug report requesting a clarification of the *usually* back in the middle of '03; It was acknowledged and filed. It would appear that they still have yet to address it. I would say for the time being (it is an unsigned long long) that it safe to assume that it always comes back in bytes.


I am getting the NSFileSystemFreeSize value back as KB. :\

----
This can get confusing. I lost some time trying to figure out why this key would not show up in the dictionary. I then discovered the method name was slightly different. But according to the documentation, they both do the same thing and should return the same keys. The first one seems to be newer and recommended in 10.5. I'm using iPhone OS 2.x.

See http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSFileManager_Class/Reference/Reference.html

The first method you find (in alphabetical order) is:

    attributesOfFileSystemForPath:error:

"Returns a dictionary that describes the attributes of the mounted file system on which a given path resides.
(...) Return Value: An NSDictionary object that describes the attributes of the mounted file system on which path resides. See �File-System Attribute Keys� for a description of the keys available in the dictionary. Availability: Available in iPhone OS 2.0 and later."

The link �File-System Attribute Keys� describes five keys, which includes NSFileSystemFreeSize. But if you run this method, it will not return that key.

The other method is:

    fileSystemAttributesAtPath:

"Returns a dictionary that describes the attributes of the mounted file system on which a given path resides.
(...) Return Value: An NSDictionary object that describes the attributes of the mounted file system on which path resides. See �File-System Attribute Keys� for a description of the keys available in the dictionary. Special Considerations: On Mac OS X v10.5 and later, use attributesOfFileSystemForPath:error: instead. Availability: Available in iPhone OS 2.0 and later."

The documentation link for the keys is the same, so according to the documentation they should both return the same keys. But if you want NSFileSystemFreeSize you can only get it with fileSystemAttributesAtPath.

