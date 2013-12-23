---
layout: page
title: GetProperFileSize
---

Hey!

I have a file that I need to get the size of.  NSFileManager does not account for resource forks, so using it is not optimal when determining and displaying an accurate file size is necessary for the user.  How can I go about retrieving the actual amount of bytes a file is, even if it contains a resource fork.  I realize that using a carbon call may be neccessary, but I have absolutely no idea as to how to go about doing this.  If anyone could provide assistantce/code I would greatly appreciate it.

----

check out     FSGetCatalogInfo()

http://developer.apple.com/documentation/Carbon/Reference/File_Manager/file_manager/chapter_1.2_section_2.html

other key words that might help are     FSRef and     FSPathMakeRef

    
FSRef ref;
Boolean isDir;
NSString *path = @"/some/path/on/your/system";
OSStatus error = FSPathMakeRef([path fileSystemRepresentation], &ref, &isDir);
if (!error) {
    /*
        Call FSGetCatalogInfo() here
    */
}



--zootbobbalu

----

"The information in this field is only valid for files.  Do not rely upon the value returned in this field for folders."

However, in general, FSGetCatalogInfo() returns bogus information on most ANY file.  I can't seem to make any sense of it, but try it yourself.  Here's an example output:

Illustrator document: whatever.eps

*Finder reports: "228 KB on disk (227,642 bytes)"
*FSGetCatalogInfo() reports:<code>
*catalogInfo.dataPhysicalSize = 0
*catalogInfo.rsrcPhysicalSize = 229376
*catalogInfo.dataLogicalSize = 0
*catalogInfo.rsrcLogicalSize = 4096</code>
*NSFileManager reports: 0 bytes (from [... fileAttributesAtPath: ...] objectForKey: [[NSFileSize] unsignedLongLongValue])


Color me confused.  If somebody can shed some light on this, please do.  I'm looking for the *exact* byte count of a file (data + rsrc).


*Well the names of the fields of the structure don't make much sense to me (but then, I've never read any documentation for that function), but the numbers do, since 229376B+4098B=228KB. It's not what you want, since it's not the exact size of the file, but it does make sense.*
----How are you calling FSGetCatalogInfo? I've never had any problems with it.

*I'm at work so I dont have the code on me, but I'm sure the call is correct.  The real problem is that this function returns "reasonable" but often incorrect values for file size.  For instance, many (and perhaps all; my testing was limited) .rtf files return 0 for all fields except rsrcLogicalSize which contains a multiple of 4096.  So, I obtain the correct size on disk, but get no information about the actual size of the file.  On the other hand. for some files the exactly byte count as reported by the Finder is returned.  In most instances it is close, and often much better than NSFileManager, but incorrect nonetheless.*

----
On Tiger the resource forks are now accessable as ExtendedFileAttributes, so using low-level C on you could get their size by:
    
int fd = open([path fileSystemRepresentation], O_RDONLY, 0);
ssize_t size = fgetxattr(fd, "com.apple.ResourceFork", NULL, 0, 0, 0x00);
close(fd);
return size;


----
Comments:

*Using     getxattr will eliminate two lines of code with, as far as I can tell, no loss of functionality.
*Is     com.apple.ResourceFork documented as being the xaddr for the resource fork? If not, then relying on it is poor form.
*No error handling. The caller had better be prepared to get a -1.

