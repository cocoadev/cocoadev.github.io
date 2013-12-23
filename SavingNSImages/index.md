---
layout: page
title: SavingNSImages
---

I have an application which saves, among other things, an NSMutableArray of NSImages which can vary in size. Unfortunately, it's generating truly massive save files. How does Cocoa save NSImages? Do I need to somehow compress them first? 

I know the problem is the images, as when I use and save a document from the app without accessing the image functionality, it's tiny (4 KB). As soon as I import images, the size shoots up.

I am loading the images with initWithContentsOfFile because I don't want lagtime in accessing them when I need them. However, right now, images which total 1.2 MB on the desktop are generating a 25.5 MB file.

----

How are you saving the images? Are you just archiving the array? Look at NSImage's     - (NSData *)TIFFRepresentationUsingCompression:(NSTIFFCompression)comp factor:(float)aFloat


----

check out NSImageToJPEG --zootbobbalu

