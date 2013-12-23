---
layout: page
title: FSSpecFromPOSIXPath
---

Before you go any farther, see the FSSpec page and do everything you can to eliminate all use of FSSpec from your application.

If you really need to get an FSSpec from a path, here's how (warning: untested code, error checking eliminated for breity):

    
FSRef fsref;
FSSpec fsspec;
OSStatus result;

result = FSPathMakeRef((UInt8*)[directory UTF8String], &fsref, nil);
result = FSGetCatalogInfo(&newRef, kFSCatInfoNone, NULL, NULL, &fsspec, NULL);


Because this code goes through an FSRef, it will only work for files which already exist. If you get an error -43 from     FSPathMakRef that means the file doesn't exist, and this code won't work.

