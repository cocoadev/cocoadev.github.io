---
layout: page
title: GettingTheRootVolumeName
---

I'm using beginSheetForDirectory to obtain a file name that will be passed in an AppleScript invocation.
For volumes OTHER than the root volume, I get something like:

/Volumes/CK OS X/Capture/Untitled 3

which, by stripping off the /Volumes/ and replacing the /s with :s becomes:

CK OS X:Capture:Untitled 3

which AppleScript understands just fine.

However, for the root volume, beginSheetForDirectory give me something like:

/Users/cklein/Documents/Untitled 3

Since the first element is not a volume name, replacing the /s with :s gives something AppleScript can't handle.
I could recognize this case and add the volume name... but I can't figure out how to GET the name of the root
volume in a system-independent way. Any suggestions?

----

Rather than creating the path manually, use CoreFoundation:

    
CFURLRef posixURL = CFURLCreateWithFileSystemPath(NULL, 
    CFSTR("/Volumes/CK OS X/Capture/Untitled 3"), kCFURLPOSIXPathStyle, false);
CFStringRef hfsPath = CFURLCopyFileSystemPath(posixURL, kCFURLHFSPathStyle);
CFShow(hfsPath);
CFRelease(hfsPath);
CFRelease(posixURL);


----

Ouch. Really complicated. I suggest you to use NSFileManager's displayNameAtPath:@"/". It should do the work.

----

I would really recommend against that. The above code is actually rather simple, and as many people would tell you, manually constructing HFS paths is rather unsafe.

----

HFS paths in general are unsafe. It's perfectly legal to have two volumes mounted with the same name, at which point HFS paths become ambiguous. If at all possible, avoid HFS paths and use aliases or something like that.

----

I'm curious as to how it would be handling volumes with the same name.
eg. Two volumes both called "Macintosh HD" are mounted as -
/Volumes/Macintosh HD
/Volumes/Macintosh HD 1
Obviously, "Macintosh HD 1" is not the name of the drive.

----

Not to end this discussion... but this was my first posting and I want to express my appreciation to those who so quickly responded with workable solutions. For this "quick hack" program, the displayNameAtPath worked just fine. Thanks again. C. Klein

----

NP for the tip. I always prefer the simplest way to do things. displayNameAtPath can be used in many situations.

Aidas

----

Just bare careful, dudes. displayName may do something different and unexpected on internationalized systems. I wouldn't use it for a real app, unless it's really only for displaying the name to the user.

----

I'd agree with the last post. If you need a HFS path do it the proper way (CFURLCopyFileSystemPath etc).

----
Indeed. WorksForMe is a very easy way to create software which breaks inexplicably on other people's computers.

