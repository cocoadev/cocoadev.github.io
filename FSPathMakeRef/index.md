---
layout: page
title: FSPathMakeRef
---

FSPathMakeRef is a function which creates an FSRef from a POSIX path. This is probably the simplest way to get an FSRef from an NSString containing a path. Example:

    
FSRef outRef;
OSStatus err = FSPathMakeRef((const UInt8 *)[path fileSystemRepresentation], &outRef, NULL);
if(err != noErr)
   ...signal the error...


Note that since FSRef<nowiki/>s can only point to existing files, this function will not work if the file indicated by the path does not exist.

----

In most cases it is better to call FSPathMakeRefWithOptions and pass kFSPathMakeRefDefaultOptions, since it will succeed for paths such as "/Volumes" unlike FSPathMakeRef.

