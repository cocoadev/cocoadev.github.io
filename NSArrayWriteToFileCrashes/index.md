---
layout: page
title: NSArrayWriteToFileCrashes
---



Hello,

When executing "writeToFile" below, my code consistently crashes (SIGSEGV), and I cannot figure out why.
All I want to do is to pick some directory (e.g., "/tmp" in the code below) and gather information about all the files within that directory.
Once that is completed, I want to save this information to file.  I don't understand, though, why this code would
crash on me.  What am I missing?


    
	NSDirectoryEnumerator *enumerator = [[NSFileManager defaultManager] enumeratorAtPath:@"/tmp"];
	NSMutableArray *array = [NSMutableArray arrayWithCapacity:50];
	while ([enumerator nextObject]) {
		[array addObject:[enumerator fileAttributes]];
	}

	[array writeToFile:@"/tmp/experiment" atomically:YES]; // crashes right here



P.S.: I am using OS X Panther

----
What is the backtrace of the crash? What are the exact contents of array?

----
Thanks for the reply.  The stack trace is as follows:

    
... [ repeat #12260 - #12258 until crash ]
#12258	0x90a9041c in -[NSCFArray isEqual:]
#12259	0x90ae7a68 in -[NSFileAttributes isEqual:]
#12260	0x901c1eb0 in CFEqual
#12261	0x90a9041c in -[NSCFArray isEqual:]
#12262	0x90ae7a68 in -[NSFileAttributes isEqual:]
#12263	0x901c1eb0 in CFEqual
#12264	0x90a9041c in -[NSCFArray isEqual:]
#12265	0x90ae7a68 in -[NSFileAttributes isEqual:]
#12266	0x901c1eb0 in CFEqual
#12267	0x901c7170 in __CFSetFindBuckets1
#12268	0x901d3904 in CFSetGetValueIfPresent
#12269	0x90a2d210 in -[NSCFSet member:]
#12270	0x90a321c4 in -[NSSet containsObject:]
#12271	0x90a31b4c in _NSIsPList
#12272	0x90a31be4 in _NSIsPList
#12273	0x90aaa73c in -[NSArray writeToFile:atomically:]
#12274	0x000d8e3c in main at main.m:12


The contents of the array is:

    
(
    {
        NSFileCreationDate = 2006-10-12 11:00:08 -0700; 
        NSFileExtensionHidden = 0; 
        NSFileGroupOwnerAccountID = 0; 
        NSFileGroupOwnerAccountName = wheel; 
        NSFileModificationDate = 2006-10-12 11:02:33 -0700; 
        NSFileOwnerAccountID = 502; 
        NSFileOwnerAccountName = xyz; 
        NSFilePosixPermissions = 488; 
        NSFileReferenceCount = 3; 
        NSFileSize = 102; 
        NSFileSystemFileNumber = 2481598; 
        NSFileSystemNumber = 234881026; 
        NSFileType = NSFileTypeDirectory; 
    }, 
    {
        NSFileCreationDate = 2006-10-12 11:02:33 -0700; 
        NSFileExtensionHidden = 0; 
        NSFileGroupOwnerAccountID = 0; 
        NSFileGroupOwnerAccountName = wheel; 
        NSFileModificationDate = 2006-10-12 11:06:32 -0700; 
        NSFileOwnerAccountID = 502; 
        NSFileOwnerAccountName = xyz; 
        NSFilePosixPermissions = 488; 
        NSFileReferenceCount = 3; 
        NSFileSize = 102; 
        NSFileSystemFileNumber = 2481628; 
        NSFileSystemNumber = 234881026; 
        NSFileType = NSFileTypeDirectory; 
    }, 
    {
        NSFileCreationDate = 2006-10-12 11:02:33 -0700; 
        NSFileExtensionHidden = 0; 
        NSFileGroupOwnerAccountID = 0; 
        NSFileGroupOwnerAccountName = wheel; 
        NSFileModificationDate = 2006-10-12 11:02:33 -0700; 
        NSFileOwnerAccountID = 502; 
        NSFileOwnerAccountName = xyz; 
        NSFilePosixPermissions = 488; 
        NSFileReferenceCount = 3; 
        NSFileSize = 102; 
        NSFileSystemFileNumber = 2481629; 
        NSFileSystemNumber = 234881026; 
        NSFileType = NSFileTypeDirectory; 
    }, 
    {
        NSFileCreationDate = 2006-10-12 11:02:33 -0700; 
        NSFileExtensionHidden = 0; 
        NSFileGroupOwnerAccountID = 0; 
        NSFileGroupOwnerAccountName = wheel; 
        NSFileModificationDate = 2006-10-12 11:02:33 -0700; 
        NSFileOwnerAccountID = 502; 
        NSFileOwnerAccountName = xyz; 
        NSFilePosixPermissions = 488; 
        NSFileReferenceCount = 2; 
        NSFileSize = 68; 
        NSFileSystemFileNumber = 2481630; 
        NSFileSystemNumber = 234881026; 
        NSFileType = NSFileTypeDirectory; 
    }, 
    {
        NSFileCreationDate = 2006-10-12 10:59:50 -0700; 
        NSFileExtensionHidden = 0; 
        NSFileGroupOwnerAccountID = 0; 
        NSFileGroupOwnerAccountName = wheel; 
        NSFileHFSCreatorCode = 0; 
        NSFileHFSTypeCode = 0; 
        NSFileModificationDate = 2006-10-12 10:59:50 -0700; 
        NSFileOwnerAccountID = 0; 
        NSFileOwnerAccountName = root; 
        NSFilePosixPermissions = 420; 
        NSFileReferenceCount = 1; 
        NSFileSize = 745; 
        NSFileSystemFileNumber = 2481588; 
        NSFileSystemNumber = 234881026; 
        NSFileType = NSFileTypeRegular; 
    }
)


----
It seems that the dictionary you get back is being uncooperative, and is a special subclass of NSDictionary. Try making normal NSDictionary objects from the array, something like this:

    
[array addObject:[NSDictionary dictionaryWithDictionary:[enumerator fileAttributes]]];


I'm not sure this is the problem but it's worth a try.
----
Are you sure you have the right write rights on that path?

otherwise try this
    
NSString *storePath = [NSHomeDirectory() stringByAppendingPathComponent:@"/tmp/experiment"];
		[arrayData writeToFile:storePath atomically:YES];


I did convert your given content into a plist, read that into a array and then wrote it to disk. No problems here, even in /tmp/.. ( if I have write rights ).

----

Thank you very much for the two suggestions!  Creating normal NSDictionary objects did the trick.

----
I recommend filing a bug about this on http://bugreport.apple.com . The interface declares the return type as NSDictionary and so they ought to be directly serializable! Whip up a small test case to post with the report, they like that.
----
Can you tell us what version of osx and xcode you did use? I did use this code in an older app to and want to be sure if I need to change it. Thanks

----
Re: 1) Good point.  Just filed a bugreport.
Re: 2) OS X 10.3.9 Build 7W98, XCode 1.2

----

So you haven't tried 10.4?

----
No,  all my machines are still running 10.3.

