---
layout: page
title: FileNameFromString
---

What is the proper way to create a valid filename from a string? For example I have a string containing "My happy :) invalid filename" and I want to convert that to something valid, namely, the colon might have to be replaced by something else.... or in other cases, length of string may be too long or the string might contain a slash. 

Does Cocoa provide something for this? Or is there at least a way to check if a filename is valid?

----

This is largely a problem in string manipulation. Check out the FoundationKit documentation on NSString. There are plenty of routines there to deal with path operations.

----
Start with NSString �fileSystemRepresentation

Then see
Working with paths 
+ pathWithComponents:

� pathComponents

� completePathIntoString:caseSensitive:matchesIntoArray:filterTypes:

� fileSystemRepresentation

� getFileSystemRepresentation:maxLength:

� isAbsolutePath

� lastPathComponent

� pathExtension

� stringByAbbreviatingWithTildeInPath

� stringByAppendingPathComponent:

� stringByAppendingPathExtension:

� stringByDeletingLastPathComponent

� stringByDeletingPathExtension

� stringByExpandingTildeInPath

� stringByResolvingSymlinksInPath

� stringByStandardizingPath

� stringsByAppendingPaths:

----

Thanks, I've been using all these other ones, just missed fileSystemRepresentation somehow!

----

So will     fileSystemRepresentation return a string that is a valid filename?

----

Technically "My happy :) invalid filename" is a valid file name, but the Finder displays the colon as as slash. So if you want to validate a filename the user entered you should switch all the "/" with ":". I'm not sure if fileSystemRepresentation takes this into consideration, but my guess is no, since it would probably interpret a path with slashes as being directories and not part of the name for the file. -PabloGomez

----

It gets worse: what's valid depends on what filesystem you're saving to. On HFS+, *#!%($@#%G&*#@$ is a valid filename. This is not going to work for FAT32. I don't think there is a good way to test if a filename is valid without simply trying to save the file, but I could be wrong. In any case,     fileSystemRepresentation won't and can't guarantee a valid name.

----

It gets even more horrible still: on an SMB share, it's possible to write a file called ".whatever". That's seems to be a valid filename according to the SMB specification and according to the Windows SMB server's interpretation of the FAT32/NTFS naming rules, but it's invalid according to the Windows Explorer, leading to a file that cannot be deleted on the Windows machine... Conclusion: file systems are horrible, and Windows file systems especially so.

----

Anyone know how to do the inverse operation of fileSystemRepresentation?  So let's say I use standard clib routines to get hold of a filename.  Now I want to create a proper NSString with the filename.  If the name is in japanese, maybe I use [stringWithCString: s encoding:NSJapaneseEUCStringEncoding].  Maybe not.  Maybe the name is in Chinese.  Any ideas?

NSFileManager has:     - (NSString *)stringWithFileSystemRepresentation:(const char *)string length:(unsigned)len

----

For a discussion about sorting batches of file names using NSString, see FilenamesArentStrings

----

If you want an HFS-style string with colons instead of slashes (for use with NSAppleScript for example) you can do this:

    
NSString* path = [(NSString*)CFURLCopyFileSystemPath((CFURLRef)url, kCFURLHFSPathStyle) autorelease];


-JoshMinor

