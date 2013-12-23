---
layout: page
title: FSCopyObject
---

FSCopyObject is obsolete Apple sample code for Mac OS 9 through Mac OS X 10.3:

http://developer.apple.com/samplecode/FSCopyObject/index.html

It contains source to demonstrate how to copy/delete files and folders with HFS+ APIs. It also demonstrates a technique to rename an object if an object of the same name exists in the destination. This sample shows how to perform these opperations in an MP-safe way.

If you are targeting 10.4 or later, there is no need for this sample code. Instead, you can use the FSPathCopyObjectSync and FSPathCopyObjectAsync APIs from Files.h.  For example usage see:

http://developer.apple.com/samplecode/FSFileOperation/index.html

