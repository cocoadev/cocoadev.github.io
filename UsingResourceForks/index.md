---
layout: page
title: UsingResourceForks
---



General/ResourceForks were common under Classic applications, but they were unsupported by any other platform than General/MacOS. I'd like to be able to modify General/ResourceForks and read data from them from Cocoa, and if possible the new .rsrc files which have the General/ResourceFork data in the General/DataFork.

Perhaps the easiest way to do this would be to create an Obj-C wrapper to the Carbon General/APIs? Anybody have any other ideas? Has someone else done it yet? Is there some class I've blatantly overlooked?

-- General/FinlayDobbie

----

You can go ahead and make the resource fork calls in your Cocoa code. An General/ObjC wrapper sounds like a fine programming experiment, but personally I don't have much use for such a thing. I want to move away from this legacy data format ...

Here's some code that illustrates opening a data-fork resource file from Cocoa. Note the use of General/NSBundle and General/NSString. This code came from a screen saver module, so I look up the class from [self class]. You'll probably want to use the app's main bundle.

    
// open our resource fork, if it exists ...
path = General/[[NSBundle bundleForClass:[self class]] pathForResource:@"resources" ofType:@"rsrc"];
if (path) {
    General/FSRef fsRef;

    if (!General/FSPathMakeRef([path UTF8String], &fsRef, NULL)) {
        General/OSErr err;

        err = General/FSOpenResourceFile(&fsRef, 0, NULL, (SInt8)fsRdPerm, &resourceRef);
        if (err) {
            General/NSLog(@"can't load '%@': %d", path, err);
            // force _resourceRef of 0 to mean we didn't open the file.
            resourceRef = 0;
        }
    } else {
        General/NSLog(@"couldn't make General/FSRef for %@", path);
    }
} else {
    General/NSLog(@"couldn't find resources.rsrc");
}


Mind you you'll still have to use the normal resource calls to actually get data out of the file. That's where your General/ObjC wrapper will get interesting.

-- General/MikeTrent

----

Oh, and once you have a nice Obj-C wrapper for resource forks, you might want to distribute the class in the General/MiscKit. See http://www.misckit.com. That's a great way (the best way?) to share this code with the Cocoa community.

-- General/MikeTrent 

----

Thanks for the hints, Mike. I might do some basic General/ResourceFork code this summer, when I have some free time. Is General/MiscKit under development any more? The last announcements seem to be from years ago, and does it even compile for OS X v10.0?

-- General/FinlayDobbie

----

I posted the source to a class, General/GTResourceFork, that encapsulates much resource fork work and is, for the most part, thread-safe. http://www.ghosttiger.com/?p=117 I welcome suggestions and improvements in the blog comments, but please, no spiced ham. :) -General/JonathanGrynspan
