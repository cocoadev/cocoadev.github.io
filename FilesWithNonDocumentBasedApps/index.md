---
layout: page
title: FilesWithNonDocumentBasedApps
---

This could be a simple question and I've overlooked it somewhere, but I've been looking for some time and cannot find an answer. I have a non-document based application, it saves data normally to disk, blah blah. What I would like to do is similar to iCal, et al. I want to be able to define file types just like a Doc-based app, which I have done. Let's say I define the iCal format in my target settings, along with an NSDocument class to go along with it. What I want to accomplish with this is to have my application convert this file into data to be stored in the library store, just as iCal does when importing iCal files. I know about NSOpenPanel/NSSavePanel and importing this way, but I want to be able to open files from the Finder and have them open in my app and import automatically, as you see in many apps. iTunes, iPhoto, iCal, etc. all do this. Is there an easy way? Do I just need to do some work in an NSDocument subclass so it doesn't bring up an interface to a document and just converts the info? Thanks for any help. --LoganCollins
----
See the TextEdit example on your hard disk.  It does everything you want and it DOESN'T use NSDocument.

----

Thanks! I think this can get me started. I see that the relevant methods to begin with are in NSApplication's delegate. This is something I hadn't thought of. --LoganCollins

