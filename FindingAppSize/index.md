---
layout: page
title: FindingAppSize
---

I am currently working on a gui wrapper for pbxbuild, and am wondering if anyone knows how I could programmatically find the size off an anygiven app within its projects build directory?

jhazelgrove

----

I think I know what you are asking. If this is a project builder build folder (e.g. /project name/build) then simply call this function to get the application build size.

NSFileManager *manager = [[NSFileManager alloc] init];

NSDictionary *fileSize = [manager fileAttributesAtPath:@"/project name/Build/appname.app" traverseLink:YES];

NSLog([NSNumber numberWithInt:[fileSize objectForKey:NSFileSize]]);

If that is not what you are looking for, then I can't really help.

I am unsure if this works on the bundle...if not, then you will have to look into another method. I did not have time to test it. I am sure there is a method to find folder size. If not, you will need to write a bit of code to hunt through all the folders inside, add the size of all the files contained and then use that.

-Mat

----


The code should work. I got it to compile without errors, by mixing it with some from Apple's documention:


NSNumber *FileSize;

NSFileManager *manager = [[NSFileManager alloc] init];

NSDictionary *fileSize = [manager fileAttributesAtPath:@"Users/jacobhaz/dev/PB Build Test1/Build/PB Build Test1.app" traverseLink:YES];

FileSize = [fileSize objectForKey:NSFileSize];

NSLog(@"File size: %d\n", [fileSize intValue]);



But when ever I test it, I get a file size of 0, no matter what I put in for it to check. I even put in a plain txt file and got 0. I messed around with it for several hours, and haven't had any luck.

jhazelgrove

----

Daniel Jalkut shared his fastFolderSizeAtFSRef function on Apple's Cocoa-Dev list: http://lists.apple.com/archives/cocoa-dev/2005/May/msg01876.html

Tutorial on the various methods of calculating a file or folder's size using Cocoa, Carbon, and UNIX alternatives: http://www.edenwaith.com/development/tutorials/foldersize/

