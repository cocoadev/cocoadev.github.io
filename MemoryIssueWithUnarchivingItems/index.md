---
layout: page
title: MemoryIssueWithUnarchivingItems
---

I have decided to broaden my Cocoa knowledge a little by creating an NSObject subclass that abides by NSCopying and NSCoding. Part of this experimentation was to provide my class with the ability to be archived to disk and unarchived, which I have succeeded except when throwing around multiple objects.

I want to copy the items into an array (they are distinct items) so I can reuse them later. I call NSKeyedUnarchiver which calls this:

    

- (id)initWithCoder:(NSCoder *)coder
{
    if (self = [super init])
    {
        _contents = [[[NSMutableDictionary alloc] initWithDictionary:[coder decodeObjectForKey:@"SVCONTENTS"]] retain]; 
        _name = coder decodeObjectForKey:@"SVNAME"] retain];
        _notes = [[coder decodeObjectForKey:@"SVNOTES"] retain];
        _type = [coder decodeIntForKey:@"SVTYPE"];        
    }
    return self;
}



My issue is, _contents, _name, _notes and _type are overwriting each other each time I add the object into an array. For instance, when I load the first object, _name might be "Bob". When I load the second, the first objects _name changes to the seconds.

How do I go about alleviating this memory issue? I'm sure its quite an easy fix, I just can't find it. I have tried copy, releasing, autoreleasing, etc. to no avail.

Thanks in advance,

-- SV

----

How do you encode/decode your array?

Show your     -encodeWithCoder and your     -save methods. It seems that the object you encode is an array, but you only show the item method, which looks OK, so the problem is somewhere else.

----

I know they are being encoded fine as when I log each item as I unarchive them, they are correct, its just adding them to the array as I am unarchiving them that is causing the issue.

Please show us the code anyway.  You probably have a memory management error.

----

Whomever SV is, you share my IP which means you are in Sydney connected via Optus. I would love to hear from you. -- [[MatPeterson
----
Are you allowing Cocoa to encode/decode the array for you, or are you doing it yourself via enumeration? It almost seems like the later, reusing a single object when doing so. Just a thought.

