---
layout: page
title: FileWrapperWithContents
---


    
NSFileWrapper *fileWrapper;
    fileWrapper = [[NSFileWrapper alloc] initWithPath:@"/wrapper.dwn"];
    if (![fileWrapper writeToFile:fileWrapperPath atomically:YES updateFilenames:NO])
        NSLog(@"Couldnt Create FileWrapper");


I use this code to create a filewrapper, i want it to act like the safari.download wrapper, but it doesnt work.

How do i make that to work.

----

FolderBasedDocuments can point you in the right direction on this. But you're just writing one file to a path in that code; you need to write a directory. See -[NSFileWrapper initDirectoryWithFileWrappers]. -- AndyMatuschak

