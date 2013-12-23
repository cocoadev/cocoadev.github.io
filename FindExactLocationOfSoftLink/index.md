---
layout: page
title: FindExactLocationOfSoftLink
---

How do I find the location of the file a soft link links to by code, made with:

    
ln -s /folder /target


----

NSFileManager is a way to go:     [[NSFileManager defaultManager] pathContentOfSymbolicLinkAtPath:@"/path/to/link"]

-- DenisGryzlov

