---
layout: page
title: FileSize
---

Is there any way to figure out how large a standard text file will be just by using the text in an NSTextView? I want to have a display showing the current size a file will be if saved at that time.

----

You can do a character count, by getting the *length* of the *stringValue* of the NSTextView instance in question.  If you're dealing with an 8 bit character code -- plain ASCII, for instance you can just multiply that times the length -- I think unicode is 16 bit, but I might be wrong... anyhow -- that's pretty much the only way I can think of.  However keep in mind that that won't really tell you how much space your file will hog up on the drive.  That will depend on how your drive is formated.

----

Yes, Apple's unichar represent an UCS-2 character and thus is 16 bit. But often files are saved as UTF8, which is a variable byte length encoding.

----

You could get an NSData object from the NSTextView's contents, and do a [dataObject length] on that.

----

I'm trying to get the file size for a file/folder, and using NSFileManager's directoryAttributesAtPath:traverseLink: works (using objectForKey:NSFileSize) but it returns the length of the file and I want/prefer to get the size it takes up on disk. For example, for a text file, Finder says it's 211 bytes, but takes 4K on disk, and the .Mac Backup program also says 4K. Is there a way to get this? NSFileManager's method returns the 211 bytes, but I want to get the 4K. Or is that unnecessary (btw I'm making a backup function in my app). --KevinWojniak

----

Finder is returning the file size expressed in a multiple of disk page sizes. IIRC from my Traditional Mac OS days, HFS and HFS+ support a variable page length scheme, depending entirely on the size of the HFS volume in question. Also, page sizes may not be the same between file types (HFS, HFS+, UFS, NFS, FAT32, AFS, AFP, etc.). So ... a complete solution would be to ask the User what string encoding they intend to use, get the size of the file in that string encoding, ask User where they intend to save the file, use statfs to find out what the fundamental block size is for that file system, and then produce a value accordingly. 

    
off_t logicalSize = FileSizeForTheUsersIntendedStringEncoding(); // you know what to do here
int blockSize = GetTheBlockSizeForTheUsersIntendedFileSystem(); // call statfs
off_t physicalSize = size / blockSize + ((size % blockSize) ? 1 : 0 );


Another solution is just to round up to 4k. 

    
off_t logicalSize = FileSizeForTheUsersIntendedStringEncoding(); // you know what to do here
off_t physicalSize = size / 4096 + ((size % 4096) ? 1 : 0 );


In all cases, just translate the sizes into kb, mb, gb, normally. 

Also, IMO, doing all this work is normally unnecessary. If you want to tell the user how big something is going to be -- it's sufficient just to return the logical size. Note the Finder prints both the logical and physical size. So why worry? OTOH, if you are trying to make sure there's enough room on disk to store your file, or if you are working on a backup or installation tool, you may want to report more accurate information; in those cases it is appropriate to return the physical size.

HTH.

-- MikeTrent

----

You can always use the trusty Carbon File Manager:
    NSString *filePath = @"...";
OSStatus err;

FSRef fileRef;
err = FSPathMakeRef([filePath fileSystemRepresentation], &fileRef, NULL);
if (err == noErr) {
    FSCatalogInfo catalogInfo;
    err = FSGetCatalogInfo(&fileRef, kFSCatInfoDataSizes, &catalogInfo, NULL, NULL, NULL);
    if (err == noErr) {
        UInt64 physicalSize = catalogInfo.dataPhysicalSize;
    }
}


----

Here's a way to get any file or folder's content size in Cocoa:

    
NSFileManager * fm = [NSFileManager defaultManager];
NSArray * contents;
unsigned long long size = 0;
NSEnumerator * enumerator;
NSString * path;
BOOL isDirectory;


// Determine Paths to Add
if ([fm fileExistsAtPath:filePath isDirectory:&isDirectory] && isDirectory) {
	contents = [fm subpathsAtPath:filePath];
} else {
	contents = [NSArray arrayWithObject:@""];
}

// Add Size Of All Paths
enumerator = [contents objectEnumerator];
while (path = [enumerator nextObject]) {
	NSDictionary *fattrs = [fm fileAttributesAtPath:[filePath stringByAppendingPathComponent:path] traverseLink:NO];
	size += fattrs objectForKey:[[NSFileSize] unsignedLongLongValue];
}

// Return Total Size in Bytes
return [NSNumber numberWithUnsignedLongLong:size];


-- Seth Willits.

