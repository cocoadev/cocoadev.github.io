---
layout: page
title: NSURLorNSStringforFiles
---

I am designing an application which may load files from ftp sites. I figured that I then better stick to exclusively using NSURL for file names throughout my program.

It turns out that NSURL suck in that I always have to do [NSURL fileURLWithPath:<nsstring>] because NSStrings are used so many places... and worse, 1) the NSApplication delegate methods all use NSStrings for opening files and 2) even though the NSDocumentController has a noteNewRecentDocumentURL (which take an NSURL) then it will choke on non-file URLs... so basically NSURLs are incompatible with the core system services I would need...

So should I just use normal NSStrings and handle ftp opens specially (i.e. not tell the document controller about it on so)?

