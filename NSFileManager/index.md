---
layout: page
title: NSFileManager
---

**NSFileManager** -- enables you to perform many generic file-system operations and insulates an application from the underlying file system.

See: http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSFileManager_Class/

----

----
**Related Topics:**

----
[Topic]

----

----
**How do you get a file or folder's icon?**

----
*See the     iconForFile: methods in NSWorkspace.*

----

----
**Copying files**

----
Quick plug for this class - it's real nice.  With this you can write an installer 'script' that feels as natural as one in perl, but has all the other goodness of cocoa (gui, etc).  Free yourself from the confines of Installer.app!

----

Quick caveat for this class - it's nice and easy to use but it's copy/move functionality only seems to work in the main thread.  The second I tried to move it to a secondary thread I had every weird problem you could imagine, segfaults, copies crapping out in the middle, etc.  If you want to copy files in OSX, I highly recommend the Apple sample code FSCopyObject (http://developer.apple.com/samplecode/Sample_Code/Files/FSCopyObject.htm ); it works in secondary threads (though it may not precisely be thread-safe), it's only slightly harder to use* and it's faster than Speedy Gonzalez.  -- Bo

* Most of the difficulty involves using FSRef's instead of paths for referring to the objects to copy.  The necessary incantation to convert them is:
    
FSRef theRef;
CFURLGetFSRef((CFURLRef)[NSURL fileURLWithPath:thePath] ,&theRef);


or even faster:

    
FSRef theRef;
FSPathMakeRef( (const UInt8 *)[thePath fileSystemRepresentation], &theRef, NULL );


----

NSFileManager also apparently uses temporary files with 59-character GUIDs, which means it breaks when working with Windows' File Services for Macintosh.

----
To copy a file from a source location to a destination location, I tried to use the NSWorkspace instance method performFileOperation with the argument of NSWorkspaceCopyOperation, as in:

    
int tag;
BOOL succeeded;
NSString *source, *destination, *fullPath;
	
source = [@"~/Documents" stringByExpandingTildeInPath];
destination = [@"~/Desktop" stringByExpandingTildeInPath];
fullPath = [@"~/Documents/fileToCopy.pdf" stringByExpandingTildeInPath];

NSWorkspace *workspace = [NSWorkspace sharedWorkspace];
NSArray *files = [NSArray arrayWithObject:fullPath];
	
succeeded  = [workspace performFileOperation:NSWorkspaceCopyOperation
                   source:source destination:destination
                   files:files 
                   tag:tag];


without any luck. If someone can see the fault of what I tried, please correct it.

But, using NSFileManager was a dream compared to NSWorkspace. To accomplish the same thing I tried above using NSFileManager consisted of the following:

    
NSFileManager *fileManager = [NSFileManager defaultManager];

NSString *source, *destination;

source = [@"~/Documents/fileToCopy.pdf" stringByExpandingTildeInPath];
destination = [@"~/Desktop/fileToCopy.pdf" stringByExpandingTildeInPath];

if ([fileManager fileExistsAtPath:source]) 
{
    [fileManager copyPath:source toPath:destination handler:nil];
}


which I pretty much copied word-for-word from Apple's documentation for NSFileManager class reference.

----
The mistake is that NSWorkspace's     files array is a list of *relative* paths (i.e. just     @"fileToCopy.pdf" in this case). The paths are interpreted relative to the source directory, of course. --JediKnil

----

[snip: *moved q&A about finding AllApplications and appended to existing discussion on that page* /snip]

